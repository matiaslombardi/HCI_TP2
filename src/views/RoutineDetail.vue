<template>
    <v-card flat class="ma-5">
        <v-row>
            <v-col cols="8">
                <v-list>
                    <v-list-item>
                        <h1 class="teal--text">{{ routine.name }}</h1>
                        <div class="ml-16">
                            <v-card-actions>
                                <v-icon>mdi-shield</v-icon>
                                <v-card-subtitle class="text-capitalize">
                                    {{ routine.difficulty }}
                                </v-card-subtitle>
                                <v-spacer></v-spacer>
                                <v-btn icon @click="manageFav()">
                                    <v-icon v-if="favorite" color="teal">mdi-heart</v-icon>
                                    <v-icon v-else>mdi-heart-outline</v-icon>
                                </v-btn>
                            </v-card-actions>
                        </div>
                    </v-list-item>
                </v-list>
                <v-card-subtitle class="mt-n5">by {{ user.username }}</v-card-subtitle>
                <v-card-text>
                    <v-row>
                        <v-rating class="ml-3 mt-1" color="teal lighten-2" background-color="teal lighten-3" hover dense
                                  length="5" size="18" v-model="rating"></v-rating>
                        <v-btn x-small rounded outlined class="ml-2 mt-1" @click="rateRoutine()" color="teal">
                            Rate now
                        </v-btn>
                    </v-row>
                </v-card-text>
            </v-col>
        </v-row>

        <v-card-text class="text--primary">{{ routine.detail }}</v-card-text>

        <v-divider class="mx-4"></v-divider>

        <v-row>
            <v-col cols="4" v-for="(cycle, index) in cycles" :key="index">
                <c-cycle-detail :cycle="cycle"></c-cycle-detail>
            </v-col>
        </v-row>
    </v-card>
</template>

<script>

import {FavoriteRoutinesStore} from "../store/favoriteRoutinesStore";
import {RoutineCyclesStore} from "../store/routineCyclesStore";
import CycleDetail from "../components/CycleDetail";
import {RoutineStore} from "../store/RoutineStore";
import {ReviewsStore} from "../store/reviewsStore";


export default {
    name: "RoutineDetail",

    components: {
        CCycleDetail: CycleDetail,
    },

    data: () => ({
        routine: {},
        user: {},
        cycles: [],
        rating: 0,
        favorite: false
    }),

    async created() {
        this.routine = await RoutineStore.getRoutine(this.$route.query.id);
        this.user = this.routine.user;
        this.favorite = await FavoriteRoutinesStore.isFavoriteRoutine(this.routine.id);
        await this.getCyclesData();
        this.rating = await ReviewsStore.getRoutineScore(this.routine.id);
    },

    methods: {

        async getCyclesData() {
            let data = {
                page: 0,
                size: 10,
                orderBy: 'id',
                direction: 'asc'
            };
            try {
                let cycles = await RoutineCyclesStore.getAllCycles(this.routine.id, data);
                this.cycles = cycles.content;
            } catch (error) {
                console.log(error);
            }
        },

        async manageFav() {
            if ((this.favorite = await FavoriteRoutinesStore.isFavoriteRoutine(this.routine.id)) === false) {
                this.favorite = true;
                try {
                    await FavoriteRoutinesStore.addToFavorites(this.routine.id);
                } catch (error) {
                    console.log(error);
                }
            } else {
                this.favorite = false;
                try {
                    await FavoriteRoutinesStore.removeFavorite(this.routine.id);
                } catch (error) {
                    console.log(error);
                }
            }
        },
        async rateRoutine() {
            try {
                await ReviewsStore.addReview(this.routine.id, this.rating);
            } catch (error) {
                console.log(error);
            }
        }
    },
}
</script>

<style scoped>

</style>