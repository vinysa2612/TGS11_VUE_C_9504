<template>
    <v-app id="inspire">
        <v-content>
            <v-container class="fill-height" fluid>
                <v-row align="center" justify="center">
                    <v-col cols="12" sm="8" md="4">
                        <v-card class="elevation-12">
                            <v-toolbar dark flat>
                                <v-toolbar-title>Login Form</v-toolbar-title>
                                    <v-spacer />
                                </v-toolbar>
                                
                            <v-card-text>
                                <v-form>
                                    <v-text-field prepend-icon="mdi-account" v-model="form.email" label="Email Login" name="login" type="text" />
                                    <v-text-field prepend-icon="mdi-lock" v-model="form.password" id="password" label="Password" name="password" type="password"/>
                                </v-form>
                            </v-card-text>
                            
                            <v-card-actions>
                                <v-spacer />
                                    <v-btn @click="login()" color="primary" bold>Login</v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-col>
                </v-row>
            </v-container>
        </v-content>
    </v-app>
</template>

<script>
export default {
    data() {
        return {   
            form: {
                email: null,
                password: null
            },
            
            user: new FormData()
        }
    },
    
    methods: {
        login() {
            var url = this.$apiUrl + "/authentication";
            this.user = new FormData();
            this.user.append("email", this.form.email);
            this.user.append("password", this.form.password);
            this.$http.post(url, this.user).then(response => {
                
                if (response.data.token) {
                    localStorage.setItem("token", response.data.token);
                    this.$router.push({ name: "UserController" });
                } else {
                    alert("Failed to Login!");
                }
            });
        }
    }
};
</script>