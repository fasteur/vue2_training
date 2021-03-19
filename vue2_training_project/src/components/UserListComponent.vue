<template>
    <div class="my-3">
        <h3 class="mb-4 text-dark">{{ title }}</h3>

        <b-table class="mx-1" striped hover :fields="fields" :items="userList">

            <template #head(checkbox)>
                    <button class="btn btn-outline-primary btn-sm" @click="clearSelected()">Clear</button>
            </template>
            <template #cell(checkbox)="data">
                <input
                    type="checkbox"
                    :checked="selectedUsers[data.item.id]"
                    name="user.name"
                    id="user.id"
                    @change="selectUser($event, data.item.id)"
                />
            </template>

            <template #cell(edit)="data">
                <button class="btn btn-outline-primary btn-sm" type="button" @click="editUser(data.item)">Edit</button>
            </template>
        </b-table>
    </div>
</template>

<script lang="ts">
import { Component, Emit, Prop, Vue } from "vue-property-decorator";
import { User } from "./../models/user.model";
import { IKeyValue } from "./../models/interfaces/key-value.interface";

@Component
export default class UserListComponent extends Vue {
    
    @Prop() public title!: string;
    @Prop() public userList!: User[];
    @Prop() public selectedUsers!: IKeyValue;

    public fields!: any[];

    data() {
        return {
            fields: [
                'checkbox',
                { key: 'name', sortable: true },
                { key: 'age', sortable: true },
                { key: 'edit', label: '' }
            ]
        }
    }

    @Emit("editUserChanges")
    public editUserChanges(user: User): User {
        return new User({ ...user });
    }

    @Emit("selectedChanges")
    public selectedChanges(newSelectedUsers: IKeyValue): IKeyValue {
        return { ...newSelectedUsers };
    }

    @Emit("clearSelected")
    public clearSelected() {
        return;
    }

    public selectUser(event: any, userId: string): void {
        const newSelectedUsers: IKeyValue = {
            ...this.selectedUsers,
            [userId]: event.target.checked,
        };
        this.selectedChanges(newSelectedUsers);
    }

    public editUser(user: User): void {
        this.editUserChanges(user);
    }
}
</script>
