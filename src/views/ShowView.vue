<script>
    import axios from 'axios'
    import tt from "@tomtom-international/web-sdk-maps"

    export default {
    name: "ShowView",
    data() {
        return {
            apartment: [],
            imageWidth: 0,
        }
    },
    methods: {
        getApartment() {
            axios.get(`http://127.0.0.1:8000/api/apartments/${this.$route.params.id}`)
            .then( response => {
                console.log(response.data.results);
                this.apartment = response.data.results;
                // this.createMap();
            }).catch( error => {
                console.log(error);
            })
        },
        getImageSize() {
            const img = this.$refs.image;
            this.imageWidth = img.clientWidth;
        },
        getRandomArbitrary(min, max) {
            return Math.random() * (max - min) + min;
        },
    },
    created() {
        this.getApartment();
    }
    }
</script>

<template>
    <div class="container mt-3">
        <div class="row">
            <div class="col-12 mb-3 d-flex justify-content-between align-items-center">
                <h1 class="fs-2">{{ apartment.title }}</h1>
                <div class="button-wrapper d-flex align-items-center">
                    <img src="../assets/img/share.svg" class="me-3 me-md-1" width="18">
                    <span class="d-none d-md-inline-block me-4 text-decoration-underline">Condividi</span>
                    <img src="../assets/img/showHeart.svg" class=" me-md-1" width="18">
                    <span class="d-none d-md-inline-block text-decoration-underline">Salva</span>
                </div>
            </div>
            <div class="col-12 d-flex mb-3" :class=" imageWidth > 1000 ? 'justify-content-center' : '' ">
                <img ref="image" :src="apartment.img_url" class="rounded-4" :class=" imageWidth > 1000 ? 'w-100' : '' " height="850" @load="getImageSize">
            </div>
            <div class="col-7">
                <div class="row">
                    <div class="col-12">
                        <p class="fs-5 fs-md-2 fw-semibold m-0">{{ apartment.address }}</p>
                        <p class="mb-1">{{ apartment.bed_number + 2 }} ospiti · {{ apartment.bed_number }} letti · {{ apartment.toilet_number }} bagni</p>
                        <span class="d-flex align-items-center ">
                            <img src="../assets/img/star.svg" height="15" class="me-1">
                            {{ getRandomArbitrary(2, 5).toPrecision(2) }}
                        </span>
                        <hr>
                    </div>
                    <div class="col-12">
                        <div class="d-flex align-items-center ">
                            <img src="../assets/img/placeholder.svg" class="rounded-circle me-3">
                            <span>
                                <p class="m-0 fw-semibold ">Nome dell'host: {{ apartment.user.name }}</p>
                                <p class="m-0 my-host">Superhost • {{ getRandomArbitrary(2, 5).toPrecision(1) }} anni di esperienza come host</p>
                            </span>
                        </div>
                        <hr>
                    </div>
                    <div class="col-12">
                        <p class="fs-4 fw-semibold ">Cosa troverai</p>
                        <div class="row">
                            <div class="col-5 d-flex mb-3" v-for="service in apartment.services">
                                <div v-html="service.image" class="my-svg me-2"></div>
                                <span>{{ service.name }}</span>
                            </div>
                        </div>
                    </div>
                    <div class="col-12">
                        <p class="fs-4 fw-semibold ">Dove sarai?</p>
                    </div>
                </div>
            </div>
            <div class="col-5">
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
hr {
    background-color: #dddddd;
}

.my-host {
    font-size: 14px;
}

.my-svg {
    width: 25px;
}
</style>