<template>
    <div class="my-3">
        <b-table class="mx-1" striped hover :fields="fields" :items="userList">

            <template #cell(editAndDelete)="data">
                <div class="d-flex flex-row justify-content-end">
                    <button class="btn btn-outline-primary btn-sm mr-2" type="button" @click="editUserChanges(data.item)">
                        {{ $t("GENERAL.EDIT") }}
                    </button>
                    <button class="btn btn-outline-primary btn-sm mr-2" type="button" @click="deleteUserChanges(data.item)">
                        {{ $t("GENERAL.DELETE") }}
                    </button>
                </div>
            </template>

        </b-table>
    </div>
</template>

<script lang="ts">
import { Component, Emit, Prop, Vue, Watch } from "vue-property-decorator";
import { User } from "./../models/user.model";
import { IKeyValue } from "./../models/interfaces/key-value.interface";

@Component
export default class UserListComponent extends Vue {
    
    @Prop() public title!: string;
    @Prop() public userList!: User[];
    @Prop() public selectedUsers!: IKeyValue;

    public fields!: any[];

    @Emit("editUserChanges")
    public editUserChanges(user: User): User {
        return user;
    }

    @Emit("selectedChanges")
    public selectedChanges(newSelectedUsers: IKeyValue): IKeyValue {
        return { ...newSelectedUsers };
    }
    
    @Emit("deleteUserChanges")
    public deleteUserChanges(user: User): User {
        return user;
    }
    
    data() {
        return {
            fields: [
                { key: 'name', sortable: true, label: `${this.$i18n.t("USER_LIST.COLUMNS.NAME")}` },
                { key: 'age', sortable: true, label: `${this.$i18n.t("USER_LIST.COLUMNS.AGE")}` },
                { key: 'editAndDelete', label: '' }
            ]
        }
    }

    // COMPUTED
    get currentLang() {
        return this.$i18n.locale
    }

    // WATCH
    /**
     * Modify the name of the column header, when currentLang has changed
     */
    @Watch("currentLang", { deep: false, immediate: true })
    private onCurrenLangChanges() {
        this.fields.find(f => f.key === 'name').label = this.$i18n.t("USER_LIST.COLUMNS.NAME").toString();
        this.fields.find(f => f.key === 'age').label = this.$i18n.t("USER_LIST.COLUMNS.AGE").toString();
    }

    // METHODS
    public selectUser(event: any, userId: string): void {
        const newSelectedUsers: IKeyValue = {
            ...this.selectedUsers,
            [userId]: event.target.checked,
        };
        this.selectedChanges(newSelectedUsers);
    }

}
</script>
