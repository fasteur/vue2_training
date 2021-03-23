<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import axios, { AxiosResponse } from "axios";
import { User } from "../models";
axios.defaults.baseURL = "https://http-vue-3f9db-default-rtdb.firebaseio.com/";

@Component
export default class UserService extends Vue {
    
    public getUsers(): Promise<AxiosResponse<any>> {
        return axios.get("/user.json");
    }

    public addUser(user: User): Promise<AxiosResponse<any>> {
        return axios.post("/user.json", user);
    }

    public deleteUser(user: User): Promise<AxiosResponse<any>> {
        return axios.delete(`/user/${user.id}.json`);
    }

    public editUser(user: User): Promise<AxiosResponse<any>> {
        return axios.put(`/user/${user.id}.json`, {
            name: user.name,
            age: user.age,
        });
    }
}
</script>