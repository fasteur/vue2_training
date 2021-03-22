<template>
    <b-container class="home">
        <h1 class="text-center mt-3 text-dark">{{ title }}</h1>

        <UserListComponent
            :title="firstListTitle"
            :user-list="userList"
            :selected-users="selectedUsers"
            @selectedChanges="onSelectedChanges($event)"
            @editUserChanges="onEditUser($event)"
            @deleteUserChanges="onDeleteUser($event)"
            @clearSelected="cleanSelected()"
        >
        </UserListComponent>

        <!-- <button
            :class="{ active: !!selectedCount, disabled: !selectedCount }"
            class="btn btn-primary"
            @click="deleteUsers()"
        >
            <span v-if="selectedCount > 1">
                Delete {{ selectedCount }} selected users
            </span>
            <span v-else-if="selectedCount === 1">
                Delete {{ selectedCount }} selected user
            </span>
            <span v-else> No selected user to delete </span>
        </button> -->

        <UserFormComponent :user="user" @formChange="changeUser($event)">
        </UserFormComponent>
    </b-container>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import UserFormComponent from "./../components/UserFormComponent.vue";
import UserListComponent from "./../components/UserListComponent.vue";
import { User } from "../models/user.model";
import { IKeyValue, UserDto } from "../models/interfaces/index";

import axios from "axios";
axios.defaults.baseURL = "https://http-vue-3f9db-default-rtdb.firebaseio.com/";

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
            firstListTitle: "First list",
            userList: [],
            user: new User({}),
            selectedUsers: {},
        };
    }

    public get selectedCount(): number {
        return Object.keys({ ...this.selectedUsers }).filter(
            (key) => this.selectedUsers[key]
        ).length;
    }

    // LIFE CYCLE HOOKS

    created() {
        this.getUsers();
    }

    // SERVICES

    private addUser(data: User) {
        axios
            .post("/user.json", data)
            .then(() => this.getUsers())
            .catch((err) => console.log("err: ", err));
    }

    private getUsers() {
        axios
            .get("/user.json")
            .then((res) => {
                if (!res || !res.data) {
                    this.userList = [];
                    return;
                }
                const newUserList = this.userListMappingFromDto(res.data);
                this.userList = [...newUserList];
            })
            .catch((err) => console.log("err: ", err));
    }

    public onDeleteUser(user: User): void {
        axios
            .delete(`/user/${user.id}.json`)
            .then(() => this.getUsers())
            .catch((err) => console.log("err: ", err));
    }

    public editUser(userToEdit: User): void {
        axios
            .put(`/user/${userToEdit.id}.json`, {
                name: userToEdit.name,
                age: userToEdit.age,
            })
            .then(() => {
                this.getUsers();
                this.user = new User({});
            })
            .catch((err) => console.log("err: ", err));
    }

    // MAPPINGS

    private userListMappingFromDto(data: UserDto): User[] {
        return Object.keys(data).reduce((acc: User[], curr: string) => {
            acc.push(
                new User({
                    id: curr,
                    name: data[curr].name,
                    age: data[curr].age,
                })
            );
            return acc;
        }, []);
    }

    // METHODS

    public onSelectedChanges(newSelectedUsers: IKeyValue): void {
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

    public onEditUser(userToEdit: User): void {
        this.user = new User({ ...userToEdit });
    }

    private isUserAlreadyExist(newUser: User): boolean {
        const alreadyExist = !!this.userList.find(
            (user) => user.name == newUser.name && user.age == newUser.age
        );
        return alreadyExist;
    }
}
</script>
