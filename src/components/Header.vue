<template>
    <div>
        <v-app-bar dense light>
            <v-toolbar-title>
                <v-img src="../assets/ProFit-Logo-text-v1.1.png" max-height="110" max-width="110"></v-img>
            </v-toolbar-title>

            <v-toolbar-items>
                <!-- optional no funciona -->
                <v-tabs fixed-tabs background-color="transparent" class="ml-16">
                    <v-tabs-slider color="teal"></v-tabs-slider>
                    <v-tab text v-for="view in views" :key="view.view">
                        <router-link class="black--text" :to="view.route">{{ view.view }}</router-link>
                    </v-tab>
                </v-tabs>
            </v-toolbar-items>

            <v-spacer></v-spacer>

            <v-toolbar-items>
                <v-avatar color="teal" class="ma-1" size="36">
                    <img :src="user.photo" alt="">
                </v-avatar>
                <v-toolbar-title class="ma-2">{{ user.firstName }}</v-toolbar-title>
                <v-menu left bottom>
                    <template v-slot:activator="{ on, attrs }">
                        <v-btn icon v-bind="attrs" v-on="on">
                            <v-icon>mdi-chevron-down</v-icon>
                        </v-btn>
                    </template>
                    <v-list>
                        <v-list-item v-for="(option) in profileInfo" :key="option.name" :to="option.route"
                                     @click="() => {}"
                                     link>
                            <v-list-item-title>{{ option.name }}</v-list-item-title>
                        </v-list-item>
                        <v-list-item @click="overlay=true">
                            <v-list-item-title>Log Out</v-list-item-title>
                        </v-list-item>
                    </v-list>
                </v-menu>
            </v-toolbar-items>
        </v-app-bar>

        <v-overlay :value="overlay" :dark="false">
            <c-confirmation-card message="log out" toPath="/Home" @confirmationClosed="overlay=false"
                                 @confirmationAccepted="logOut"></c-confirmation-card>
        </v-overlay>
    </div>
</template>

<script>
import {UserStore} from "../store/userStore";
import ConfirmationCard from "./ConfirmationCard";
import {router} from "../main";

export default {
    name: "Header",
    components: {
        CConfirmationCard: ConfirmationCard,
    },

    data: () => ({
        user: {
            firstName: '',
            photo: '',
        },
        views: [
            {route: '/Home', view: 'Home'},
            {route: '/Workouts', view: 'Workouts'},
        ],
        profileInfo: [
            {route: '/Profile', name: 'My Profile'},
            {route: '/Routines', name: 'My Routines'},
            {route: '/Exercises', name: 'My Exercises'}
        ],
        overlay: false
    }),

    created() {
        this.getUserData();
    },

    methods: {
        async logOut() {
            this.overlay = false;
            try {
                await UserStore.logOutUser();
            } catch (error) {
                console.log(error);
            }
            this.$root.$emit('UserStatusChanged');
            await router.replace('/');
        },

        async getUserData() {
            try {
                let userInfo = await UserStore.getCurrentUserData();
                this.user.firstName = userInfo.firstName;
                this.user.photo = userInfo.avatarUrl;
            } catch (error) {
                console.log(error);
            }
        }
    },
}
</script>

<style scoped>
div[role=tab] a {
    text-decoration: none;
}
</style>