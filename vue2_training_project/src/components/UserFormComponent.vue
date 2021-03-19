<template>
    <div>
        <form @submit.prevent="submitForm()">
            <input type="text" v-model="form.name" placeholder="Ex: Florian" />
            <input type="text" v-model.number="form.age" placeholder="Ex: 28" />

            <button type="submit">Submit</button>
        </form>
    </div>
</template>

<script lang="ts">

import { Component, Emit, Prop, Vue, Watch } from "vue-property-decorator";
import { User } from "../models/user.model";

@Component
export default class UserFormComponent extends Vue {
  
    @Prop() user!: User;

    public form!: { name: string, age: number | null };

    data() {
        return {
            form: {
                name: "",
                age: null,
            },
        };
    }

    // WATCH

    @Watch("user", { deep: false, immediate: true })
    public onUserChanges(val: User) {
        this.form = {
            name: val.name,
            age: val.age,
        };
    }

    // EMIT

    @Emit("formChange")
    public formChange() {
        return {
            ...this.user,
            ...this.form,
        };
    }

    // METHODS
    
    public submitForm(): void {
        this.formChange();
        this.resetForm();
    }

    public resetForm(): void {
        this.form = {
            name: "",
            age: null,
        };
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
