<template>
    <div>
        <h3>{{ title }}</h3>
        <div v-for="user in userList" :key="user.id">
            <input type="checkbox" :checked="selectedUsers[user.id]" name="user.name" id="user.id"
                @change="selectUser($event, user.id)">
            <label for="user.name">{{ user.name }} ({{ user.age }})</label>
            <button type="button" @click="editUser(user)">Edit</button>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Emit, Prop, Vue } from "vue-property-decorator";
import { User } from './../models/user.model';
import { IKeyValue } from './../models/interfaces/key-value.interface';

@Component
export default class UserListComponent extends Vue {

    @Prop() public title!: string;
    @Prop() public userList!: User[];
    @Prop() public selectedUsers!: IKeyValue;
    
    @Emit('editUserEvent')
    public editUserEvent(user: User): User {
        return new User({ ...user });
    }
    
    public selectUser(event: any, userId: string): void {
        const newSelectedUsers = { ...this.selectedUsers, [userId]: event.target.checked };
        this.$emit('selected', { ...newSelectedUsers });
    }

    public editUser(user: User): void {
        this.editUserEvent(user);
    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
