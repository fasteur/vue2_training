<template>
    <b-form class="my-5" @submit.prevent="submitForm()">

        <h3 class="text-dark">Add or edit user</h3>
        
        <b-container class="my-4" fluid>
            <b-row>
                <b-col>
                    <b-input type="text" v-model="form.name" placeholder="Ex: Florian" />
                </b-col>
                <b-col>
                    <b-input type="text" v-model.number="form.age" placeholder="Ex: 28" />
                </b-col>
                <b-col>
                    <b-button 
                        variant="primary"
                        :class="{'active': verifyForm, 'disabled': !verifyForm}"
                        type="submit">
                        Submit
                    </b-button>
                </b-col>
            </b-row>
        </b-container>
    </b-form>
</template>

<script lang="ts">

import { Component, Emit, Prop, Vue, Watch } from "vue-property-decorator";
import { User } from "../models/user.model";

@Component
export default class UserFormComponent extends Vue {
  
    @Prop() user!: User;

    public form!: { name: string, age: number | null };
    public errors!: string[];

    data() {
        return {
            form: {
                name: "",
                age: null,
            },
            errors: []
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

    // COMPUTED

    public get verifyForm(): boolean {
        this.errors = [];

        if (!(this.form.name)) {
            this.errors.push('Name field is required!');
        }
        if (!this.form.age) {
            this.errors.push('Age is required!');
        }

        return !(this.errors.length > 0);
    }

    // METHODS
    
    public submitForm(): void {
        if (this.verifyForm) {
            this.formChange();
            this.resetForm();
        }
    }

    public resetForm(): void {
        this.form = {
            name: "",
            age: null,
        };
    }

}
</script>
