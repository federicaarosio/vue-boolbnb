<script>
    import axios from 'axios';

    export default {
    name: "HomeView",
    data() {
        return {
            apartments: [],
            range: 20,
            rooms: 0,
            beds: 0,
            services: [],
            filteredServices: [],
        }
    },
    watch: {
        address(newAddress, oldAddress) {
            this.getApartments(this.rooms, this.beds, this.filteredServices, this.address, this.range);
        }
    },
    methods: {
        getApartments(rooms, beds, services, address, range) {
            axios.get('http://127.0.0.1:8000/api/apartments', {
                params: {
                    rooms: rooms,
                    beds: beds,
                    services: services,
                    address: address,
                    range: range
                }
            })
            .then( response => {
                // console.log(response.data.results);
                this.apartments = response.data.results;
            })
            .catch(function (error) {
                console.log(error);
            });
        },
        getServices() {
            axios.get('http://127.0.0.1:8000/api/services', {
                params: {
                }
            })
            .then( response => {
                // console.log(response.data.results);
                this.services = response.data.results;
            })
            .catch(function (error) {
                console.log(error);
            });
        },
        getRandomArbitrary(min, max) {
            return Math.random() * (max - min) + min;
        },
        roomChange(room) {
            this.rooms = room;
        },
        bedChange(bed) {
            this.beds = bed;
        },
    },
    props: {
        address: String,
    },
    created() {
        this.getServices();
        this.getApartments(this.rooms, this.beds, this.filteredServices, this.address, this.range);
    }
    }
</script>

<template>
    <div class="container-fluid mt-4 px-sm-2 px-md-4 px-xl-5 my_container">
        <div class="row justify-content-center ">
            <div class="col-12 mb-4 d-flex justify-content-end ">
                <button class="my-btn rounded d-flex align-items-center" data-bs-toggle="modal" data-bs-target="#modal">
                    <img src="../assets/img/filter.svg" class="me-1">
                    Filtri
                </button>
                <!-- Modal -->
                <div class="modal fade" id="modal" tabi="-1" aria-labelledby="modalLabel" aria-hidden="true">
                    <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
                        <div class="modal-content">
                        <div class="modal-header">
                            <h1 class="modal-title fs-5" id="modalLabel">Filtri</h1>
                        </div>
                        <div class="modal-body">
                            <h2 class="fs-3 mb-4">Distanza di Ricerca</h2>
                            <div class="col-12 mb-4">
                                <label for="range" class="form-label mb-3">Distanza: <span class="primary-color fw-bold ">{{ range }} km</span></label>
                                <input type="range" v-model="range" class="form-range" min="20" max="50" step="5" id="range">
                            </div>
                            <hr>
                            <h2 class="fs-3 mb-4">Stanze e letti</h2>
                            <p>Stanze</p>
                            <div class="button-wrapper d-flex mb-4">
                                <button class="btn" :class="this.rooms == 0 ? 'active' : '' " @click="roomChange(0)">Qualsiasi</button>
                                <button v-for="i in 8" class="btn" :class="this.rooms == i ? 'active' : '' " @click="roomChange(i)">{{ i }}</button>
                            </div>
                            <p>Letti</p>
                            <div class="button-wrapper d-flex mb-4">
                                <button class="btn" :class="this.beds == 0 ? 'active' : '' " @click="bedChange(0)">Qualsiasi</button>
                                <button v-for="i in 8" class="btn" :class="this.beds == i ? 'active' : '' " @click="bedChange(i)">{{ i }}</button>
                            </div>
                            <hr>
                            <h2 class="fs-3 mb-4">Servizi</h2>
                            {{ filteredServices }}
                            <div class="row">
                                <div class="col-5 mb-3" v-for="service in services">
                                    <div class="form-check">
                                        <input class="form-check-input my-check" type="checkbox" v-model="filteredServices" :value="service.id" :id="'Check-' + service.id">
                                        <label class="form-check-label" :for="'Check-' + services.id">
                                            {{ service.name }}
                                        </label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn" data-bs-dismiss="modal" @click="getApartments(this.rooms, this.beds, this.filteredServices, this.address, this.range);">Mostra</button>
                        </div>
                        </div>
                    </div>
                </div>
            </div>
            <div v-if=" apartments == '' " class="d-flex justify-content-center ">
                <p class="fs-2">Nessun risultato disponibile</p>
            </div>
            <div class="col-11 col-md-6 col-lg-4 col-xl-3 col-xxl-2 mb-3" v-for="apartment in apartments">
                <div class="my-card" @click="$router.push({ name: 'show', params: { id: apartment.id} })">
                    <div class="img-wrapper position-relative mb-2 rounded-4 overflow-hidden ">
                        <img :src="apartment.img_url" class="img-fluid rounded-4 card-cover">
                        <div class="position-absolute top-0 start-0 ms-3 mt-3 badge rounded-pill text-bg-light p-2 my-badge">
                            Amato dagli ospiti
                        </div>
                        <svg xmlns="http://www.w3.org/2000/svg" class="position-absolute top-0 end-0 me-3 mt-3 my-heart" viewBox="0 0 32 32" aria-hidden="true" role="presentation" focusable="false">
                            <path d="M16 28c7-4.73 14-10 14-17a6.98 6.98 0 0 0-7-7c-1.8 0-3.58.68-4.95 2.05L16 8.1l-2.05-2.05a6.98 6.98 0 0 0-9.9 0A6.98 6.98 0 0 0 2 11c0 7 7 12.27 14 17z"></path>
                        </svg>
                    </div>
                    <div class="text-wrapper position-relative">
                        <p class="mb-0 my-address">{{ apartment.address }}</p>
                        <p class="text-secondary fs-6">Offerto da {{ apartment.user.name }}</p>
                        <p></p>
                        <span class="position-absolute top-0 end-0">
                            <img src="../assets/img/star.svg" height="15">
                            {{ getRandomArbitrary(2, 5).toPrecision(2) }}
                        </span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
@use '../assets/scss/partials/variables' as *;


.form-range::-webkit-slider-thumb {
    background-color: $primary-color !important;
    box-shadow: none;
    
    &:active {
        box-shadow: 0 0 0 0.25rem rgba(159, 145, 204, 0.3) !important;
    }

    &:focus {
        box-shadow: 0 0 0 0.25rem rgba(159, 145, 204, 0.3) !important;
    }
    &:checked {
        box-shadow: 0 0 0 0.25rem rgba(159, 145, 204, 0.3) !important;
    }
    &:enabled {
        
        box-shadow: 0 0 0 0.25rem rgba(159, 145, 204, 0.3) !important;
    }
}


.my-check {

    &:hover {
        border-color: $primary-color;
    }

    &:checked {
        border-color: $primary-color;
        background-color: $primary-color;
    }

    &:focus {
        border-color: $primary-color;
        outline: 0;
        box-shadow: 0 0 0 0.25rem rgba(159, 145, 204, 0.3);
    }
}

.modal {
    --bs-modal-width: 800px;
}

.modal-dialog {

    #modalLabel {
        color: $primary-color;
        font-weight: 600;
    }

    .modal-body {

        button {
            padding: 7px 23px;
            margin-right: 0.5rem;
            border: 1px solid #e7e7e7;
            border-radius: 25px;

            &:hover {
                border-color: $primary-color;
            }
        }
    }

    .modal-footer button {
        background-color: $primary-color;
        color: white;
    }
}

.card-cover {
    object-fit: cover;
    object-position: center;
    aspect-ratio: 1 / 1;
    transition: all 0.3s ease-in;

    &:hover {
        transform: scale3d(1.1, 1.1, 1.1);
    }
}

.my-heart {
    height: 25px;
    display: block; 
    fill: rgba(0, 0, 0, 0.5);
    stroke: #FFFF; 
    stroke-width: 2; 
    overflow: visible;

    &:hover {
        fill: $primary-color;
    }

    &:active {
        fill: $primary-color;
    }
}

.my-badge {
    font-weight: 500;
    font-size: 14px;
    box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
}

.my-address {
    font-weight: 600;
    color: $text-color;
    line-height: 20px;
}

.my-btn {
    padding: 10px 15px;
    border: 1px solid #e7e7e7;
    background-color: transparent;
    font-size: 13px;

    img {
        width: 15px;
        height: 15px;
    }
}

</style>
