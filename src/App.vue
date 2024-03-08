<script>
    import AppHeader from '@/components/AppHeader.vue'

    export default {
    name: "AppVue",
    components: {
        AppHeader,
    },
    data() {
        return {
            address: '',
        }
    },
    methods: {
        addressFilterHandler(address) {
            this.address = address;
        }
    }
    }
</script>

<template>
    <AppHeader @addressFilter="addressFilterHandler"/>
    <RouterView :address="address"/>
</template>

<style lang="scss" scoped>

</style>