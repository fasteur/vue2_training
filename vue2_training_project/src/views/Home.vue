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

        <UserFormComponent :user="user" @formChange="changeUser($event)">
        </UserFormComponent>
    </b-container>
</template>

<script lang="ts">
import { Component, Inject, Vue } from "vue-property-decorator";

import UserFormComponent from "./../components/UserFormComponent.vue";
import UserListComponent from "./../components/UserListComponent.vue";
import { User } from "../models/index";
import { IKeyValue } from "./../models/interfaces/key-value.interface";
import { UserDto } from "./../models/interfaces/dto/user-dto.interface";
import UserService from '../services/user.service.vue';

@Component({
    components: {
        UserFormComponent,
        UserListComponent,
    }
})
export default class Home extends Vue {

    // SERVICES
    @Inject() private userService!: UserService;

    // DATAS
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

    // COMPUTED
    public get selectedCount(): number {
        return Object.keys({ ...this.selectedUsers }).filter(
            (key) => this.selectedUsers[key]
        ).length;
    }

    // LIFE CYCLE HOOKS
    created() {
        this.getUsers();
    }


    // METHODS
    private addUser(data: User) {
        this.userService.addUser(data)
            .then(() => this.getUsers())
            .catch((err) => console.log("err: ", err));
    }

    private getUsers() {
        this.userService.getUsers()
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
        this.userService.deleteUser(user)
            .then(() => this.getUsers())
            .catch((err) => console.log("err: ", err));
    }

    public editUser(userToEdit: User): void {
        this.userService.editUser(userToEdit)
            .then(() => {
                this.getUsers();
                this.user = new User({});
            })
            .catch((err) => console.log("err: ", err));
    }

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
