<template>
    <div class="home">
        <h1>{{ title }}</h1>

        <button @click="cleanSelected()">Unselect all</button>

        <UserListComponent
            :title="'Liste 1'"
            :user-list="userList"
            :selected-users="selectedUsers"
            @selected="selectedChange($event)"
            @editUserEvent="addUserToEdit($event)"
        >
        </UserListComponent>

        <br />
        <UserFormComponent
            :user="user"
            @formChange="changeUser($event)"
        ></UserFormComponent>
        <br />

        <button @click="deleteUsers()">Delete user(s)</button>
    </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import UserFormComponent from "./../components/UserFormComponent.vue";
import UserListComponent from "./../components/UserListComponent.vue";
import { User } from "../models/user.model";
import { IKeyValue } from "../models/interfaces/key-value.interface";

@Component({
    components: {
        UserFormComponent,
        UserListComponent,
    },
})
export default class Home extends Vue {

    public title!: string;
    public userList!: User[];
    public user!: User;
    public selectedUsers!: IKeyValue;

    data() {
        return {
            title: "Listes d'utilisateurs",
            userList: [
                { name: "Florian", age: 28, id: this.getRndInteger() },
                { name: "Gwen", age: 31, id: this.getRndInteger() },
                { name: "Matthias", age: 28, id: this.getRndInteger() },
                { name: "Patty", age: 29, id: this.getRndInteger() },
            ],
            user: new User({ name: "Jules", age: 29, id: this.getRndInteger() }),
            selectedUsers: {},
        };
    }

    // METHODS

    public selectedChange(newSelectedUsers: IKeyValue): void {
        this.selectedUsers = { ...newSelectedUsers };
    }

    public cleanSelected(): void {
        this.selectedUsers = {};
    }

    public changeUser(newUser: User): void {
        if (!newUser || (newUser && !(newUser.name || newUser.age))) return;
        if (newUser && newUser.id) return this.editUser(newUser);
        if (this.isUserAlreadyExist(newUser)) return;
        this.addUser(newUser);
    }

    private getRndInteger(): string {
        return Math.floor(Math.random() * 100000000000000000000).toString();
    }

    public deleteUsers(): void {
        const idsToDelete: string[] = Object.keys(this.selectedUsers).filter(
            (v) => this.selectedUsers[v]
        );
        if (idsToDelete.length > 0) {
            this.userList = idsToDelete.reduce(
                (acc: User[], curr: string) => acc.filter((u) => u.id != curr),
                this.userList
            );
        }
    }

    public addUserToEdit(userToEdit: User): void {
        this.user = new User({ ...userToEdit });
    }

    public editUser(userToEdit: User): void {
        this.userList = this.userList.reduce((acc: User[], curr: User) => {
            if (userToEdit.id === curr.id) {
                acc.push(userToEdit);
            } else {
                acc.push(curr);
            }
            return [...acc];
        }, []);
        this.user = new User({});
    }

    public addUser(newUser: User): void {
        const userToAdd = {
            ...newUser,
            id: this.getRndInteger(),
        };
        this.userList.push(userToAdd);
    }

    private isUserAlreadyExist(newUser: User): boolean {
        const alreadyExist = !!this.userList.find((user) => user.name == newUser.name && user.age == newUser.age);
        return alreadyExist;
    }
}
</script>
