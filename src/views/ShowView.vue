<script>
    import axios from 'axios'
    import tt from "@tomtom-international/web-sdk-maps";

    export default {
    name: "ShowView",
    data() {
        return {
            apartment: [],
            imageWidth: 0,
            messageData: {
                name: '',
                surname: '',
                email: '',
                content: '',
                apartment_id: '',
            },
            messageResponse: ''
        }
    },
    methods: {
        getApartment() {
            axios.get(`http://127.0.0.1:8000/api/apartments/${this.$route.params.id}`)
            .then( response => {
                console.log(response.data.results);
                this.apartment = response.data.results;
            }).catch( error => {
                console.log(error);
            })
        },
        createMap() {
            const map = tt.map({
                key: "rBePA1fHaJT71C0Mp1YWQFMD9dcMym9E",
                container: "map",
                center: [9.187319, 45.469114],
                zoom: 17
            });
            map.addControl(new tt.FullscreenControl());
            map.addControl(new tt.NavigationControl());
            const marker = new tt.Marker({
                color: '#9f91cc',
            }).setLngLat([9.187319, 45.469114]).addTo(map);
        },
        getImageSize() {
            const img = this.$refs.image;
            this.imageWidth = img.naturalWidth;
        },
        getRandomArbitrary(min, max) {
            return Math.random() * (max - min) + min;
        },
        async sendMessage() {
            this.messageData.apartment_id = this.apartment.id;
            try {
                const response = await axios.post('http://127.0.0.1:8000/api/messages', this.messageData);
                console.log(response);
                this.messageResponse = response.data;
            } catch (error) {
                console.error(error);
            }
        }
    },
    created() {
        this.getApartment();
    },
    mounted() {
        this.createMap();
    }
    }
</script>

<template>
    <div class="container mt-3">
        <div class="row">
            {{ messageResponse }}
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
                <img ref="image" :src="apartment.img_url" class="rounded-4 object-fit-cover " :class=" imageWidth > 1000 ? 'w-100' : '' " height="750" @load="getImageSize">
            </div>
            <div class="col-7">
                <div class="row">
                    <div class="col-12">
                        <p class="fs-5 fs-md-2 fw-semibold m-0 pt-2">{{ apartment.address }}</p>
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
                        <p class="fs-4 fw-semibold ">Descrizione</p>
                        <p class="pb-3">{{ apartment.description }}</p>
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
                </div>
            </div>
            <div class="col-5 py-2 ps-5">
                <div class="card my-card ms-3 me-1 px-1">
                    <div class="card-body">
                        <p class="fs-4 fw-semibold mb-3">Contattaci</p>
                        <p class="">Siamo entusiasti di farti conoscere il nostro nuovo appartamento disponibile! Per dettagli e domande, contattaci al più presto.</p>
                        <button class="btn w-100 btn-lg my-bg-primary" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasRight" aria-controls="offcanvasRight">Invia un messaggio</button>

                        <div class="offcanvas offcanvas-end" tabindex="-1" id="offcanvasRight" aria-labelledby="offcanvasRightLabel">
                            <div class="offcanvas-header">
                                <h5 class="offcanvas-title primary-color" id="offcanvasRightLabel">Contattaci</h5>
                                <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
                            </div>
                            <div class="offcanvas-body">
                                <div class="row">
                                    <div class="col-6 mb-3">
                                        <label for="exampleDropdownFormEmail2" class="form-label">Nome</label>
                                        <input type="email" class="form-control" v-model="messageData.name">
                                    </div>
                                    <div class="col-6">
                                        <label for="exampleDropdownFormEmail2" class="form-label">Cognome</label>
                                        <input type="email" class="form-control" v-model="messageData.surname">
                                    </div>
                                    <div class="col-12 mb-3">
                                        <label for="exampleDropdownFormEmail2" class="form-label">Email</label>
                                        <input type="email" class="form-control" v-model="messageData.email">
                                    </div>
                                    <div class="col-12 mb-3">
                                        <label for="exampleFormControlTextarea1" class="form-label">Messaggio</label>
                                        <textarea class="form-control" v-model="messageData.content" rows="3"></textarea>
                                    </div>
                                    <div class="col-12">
                                        <button type="submit" class="btn my-bg-primary" @click="sendMessage()">Invia</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <p class="text-center mt-3 mb-0 fs-6 ">Contattaci senza impegno!</p>
                    </div>
                </div>
            </div>
            <div class="row pb-5 ">
                <div class="col-12">
                    <hr>
                    <p class="fs-4 fw-semibold mt-4 mb-3">Dove sarai?</p>
                    <div id="map"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.offcanvas.offcanvas-end {
    width: 500px;
}

hr {
    background-color: #dddddd;
}

.my-host {
    font-size: 14px;
}

.my-svg {
    width: 25px;
}

#map {
    width: 100%;
    height: 500px;
}

.my-card {
    border: 1px solid #dddddd;
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
}

.btn:hover {
    background-color: #9282c2;
    color: white;
}
</style>