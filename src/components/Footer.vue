<template>
    <v-footer dark padless class="index">
        <v-card class="flex #232931" flat tile>
            <v-card-title class="#232931">
                <v-row>
                    <v-col cols="8" class="text-center" offset="2">
                        <strong class="subheading ">© 2021 ProFit - All rights reserved.</strong>
                    </v-col>
                    <v-col cols="2">
                        <v-btn v-for="icon in icons" :key="icon" class="mx-xl-4 mx-lg-2" dark icon>
                            <v-icon size="24px">
                                {{ icon }}
                            </v-icon>
                        </v-btn>
                    </v-col>
                </v-row>
            </v-card-title>
        </v-card>
    </v-footer>
</template>

<script>
export default {
    name: "Footer",

    data: () => ({
        icons: [
            'mdi-facebook',
            'mdi-instagram',
            'mdi-twitter',
        ],
    }),
}
</script>

<style scoped>

</style>