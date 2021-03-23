<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import { AxiosResponse } from "axios";
import { User } from "../models";
import { myAxios } from "./../api/axios";

@Component
export default class UserService extends Vue {

    public getUsers(): Promise<AxiosResponse<any>> {
        return myAxios.get("/user.json");
    }

    public addUser(user: User): Promise<AxiosResponse<any>> {
        return myAxios.post("/user.json", user);
    }

    public deleteUser(user: User): Promise<AxiosResponse<any>> {
        return myAxios.delete(`/user/${user.id}.json`);
    }

    public editUser(user: User): Promise<AxiosResponse<any>> {
        return myAxios.put(`/user/${user.id}.json`, {
            name: user.name,
            age: user.age,
        });
    }
}
</script>