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
                messageResponse: '',
                success: false,
            }
        },
        methods: {
            getApartment() {
                axios.get(`http://127.0.0.1:8000/api/apartments/${this.$route.params.id}`)
                .then( response => {
                    // console.log(response.data.results);
                    this.apartment = response.data.results;
                }).catch( error => {
                    console.log(error);
                })
            },
            createMap() {
                const center = [this.apartment.longitude, this.apartment.latitude];
                const map = tt.map({
                    key: "rBePA1fHaJT71C0Mp1YWQFMD9dcMym9E",
                    container: "map",
                    center: center,
                    zoom: 17
                });
                map.addControl(new tt.FullscreenControl());
                map.addControl(new tt.NavigationControl());
                const marker = new tt.Marker({
                    color: '#9f91cc',
                }).setLngLat(center).addTo(map);
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
                    this.success = response.data.success;
                    this.messageData = [];
                } catch (error) {
                    console.error(error);
                }
            },
            closeMessage() {
                this.success = false;
            },
            Validation() {
                const inputList = document.getElementsByClassName('validation');
                const inputArray = Array.from(inputList);
                let validated = true;
                inputArray.forEach((element, index) => {
                    if(element.value == '') {
                        element.classList.add('is-invalid');
                        let errorEl = document.createElement('span');
                        errorEl.classList.add('invalid-feedback');
                        errorEl.textContent = 'Inserire un campo valido!';
                        element.insertAdjacentElement('afterend', errorEl);
                        element.parentNode.childElementCount > 3 ? element.parentNode.removeChild(inputList[index].parentNode.lastChild) : '';
                        validated = false;
                    }
                });
                if(validated) {
                    const errorElList = document.getElementsByClassName('invalid-feedback');
                    const errorElArray = Array.from(errorElList);
                    errorElArray.forEach( element => {
                        element.classList.add('d-none');
                    });
                    inputArray.forEach( element => {
                        element.classList.remove('is-invalid');
                    });
                    this.sendMessage();
                };
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
    <div class="messageNav position-fixed bottom-0 w-100 bg-white d-flex justify-content-between align-items-center p-3 z-3 d-md-none">
        <p class="fs-4 fw-semibold m-0">Contattaci</p>
        <button class="btn btn-lg my-bg-primary rounded-4" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasRight" aria-controls="offcanvasRight">Invia un messaggio</button>
    </div>
    <div class="container-fluid container-md mt-3">
        <div class="row">
            <div class="col-12 mb-3 d-flex justify-content-between align-items-center">
                <h1 class="fs-2 d-none d-md-block">{{ apartment.title }}</h1>
                <div class="fs-3 fw-semibold ps-1 link-underline-opacity-0 link-underline my-text-primary d-md-none" @click="$router.push({ name: 'home' })"> < </div>
                <div class="button-wrapper d-flex align-items-center">
                    <img src="../assets/img/share.svg" class="me-3 me-md-1" width="18">
                    <span class="d-none d-md-inline-block me-4 text-decoration-underline">Condividi</span>
                    <img src="../assets/img/showHeart.svg" class=" me-md-1" width="18">
                    <span class="d-none d-md-inline-block text-decoration-underline">Salva</span>
                </div>
            </div>
            <div class="col-12 d-flex mb-3" :class=" imageWidth > 1000 ? 'justify-content-center' : '' ">
                <img ref="image" :src="apartment.img_url" class="rounded-4 object-fit-cover my-img" :class=" imageWidth > 1000 ? 'w-100' : '' " height="750" @load="getImageSize">
            </div>
            <div class="col-12 col-md-7">
                <div class="row">
                    <div class="col-12">
                        <p class="fs-3 fs-md-2 fw-semibold m-0 d-md-none mb-1">
                            {{ apartment.title }} 
                            <span class="badge rounded-pill my-bg-primary ms-1" v-if=" apartment.sponsors != '' ">Sponsorizzato</span>
                        </p>
                        <p class="fs-6 fw-semibold m-0">
                            {{ apartment.address }} 
                            <span class="badge rounded-pill my-bg-primary ms-1 d-none d-md-inline" v-if=" apartment.sponsors != '' ">Sponsorizzato</span>
                        </p>
                        <p class="mb-1">{{ apartment.bed_number + 2 }} ospiti · {{ apartment.bed_number }} letti · {{ apartment.toilet_number }} bagni</p>
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
            <div class="d-none d-md-block col-5 py-2 ps-5">
                <div class="card position-relative my-card ms-3 me-1 px-1">
                    <div class="card-body">
                        <div class="svg">
                            <img src="../assets/img/shape.svg">
                        </div>
                        <p class="fs-4 fw-semibold mb-3">Contattaci</p>
                        <p class="">Siamo entusiasti di farti conoscere il nostro nuovo appartamento disponibile! Per dettagli e domande, contattaci al più presto.</p>
                        <button class="btn btn-lg my-bg-primary rounded-4 mt-2" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasRight" aria-controls="offcanvasRight">Invia un messaggio</button>
                    </div>
                </div>
            </div>
            <!-- Offcanvas -->
            <div class="offcanvas offcanvas-end" tabindex="-1" id="offcanvasRight" aria-labelledby="offcanvasRightLabel">
                <div class="offcanvas-header">
                    <h5 class="offcanvas-title primary-color" id="offcanvasRightLabel">Contattaci</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
                </div>
                <div class="offcanvas-body">
                    <div class="row">
                        <div class="col-12" v-if=" success == true">
                            <div class="alert my-alert d-flex justify-content-between fw-semibold " role="alert">
                                Messaggio inviato con successo!
                                <img src="../assets/img/x.svg" class="my-close-button" width="18" @click="closeMessage">
                            </div>
                        </div>
                        <div class="col-6 mb-3">
                            <label for="exampleDropdownFormEmail2" class="form-label">Nome</label>
                            <input type="email" class="form-control validation" v-model="messageData.name">
                        </div>
                        <div class="col-6">
                            <label for="exampleDropdownFormEmail2" class="form-label">Cognome</label>
                            <input type="email" class="form-control validation" v-model="messageData.surname">
                        </div>
                        <div class="col-12 mb-3">
                            <label for="exampleDropdownFormEmail2" class="form-label">Email</label>
                            <input type="email" class="form-control validation" v-model="messageData.email">
                        </div>
                        <div class="col-12 mb-3">
                            <label for="exampleFormControlTextarea1" class="form-label">Messaggio</label>
                            <textarea class="form-control validation" v-model="messageData.content" rows="3"></textarea>
                        </div>
                        <div class="col-12">
                            <button type="submit" class="btn my-bg-primary" @click="Validation">Invia</button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col-12 pb-5 mb-5">
                <hr>
                <p class="fs-4 fw-semibold mt-4 mb-3">Dove sarai?</p>
                <div id="map"></div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
@use '../assets/scss/app.scss' as *;
@use '../assets/scss/partials/variables' as *;

@media screen and (max-width: 576px) {
    .my-img {
        height: auto;
    }
}

.my-alert {
    background-color: rgba(146, 130, 194, 0.6);
    color: rgb(255, 255, 255);
}

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
    height: 100%;
    max-height: 450px;
    border: 1px solid #dddddd;
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
    background-image: url('../assets/img/card-bg.jpg');
    background-size: cover;
    background-position-y: 250px;
    background-repeat: no-repeat;
}

.btn:hover {
    background-color: #9282c2;
    color: white;
}

.svg {
    width: 80px;
    height: 80px;
    margin-bottom: 0.75rem;

    background-color: $primary-color;

    border-radius: 25px;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: 0px 3px 8px rgba(146, 130, 194, 0.6);

    img {
        width: 52px;
        height: 52px;
    }
}

.messageNav {
    border-top: 1px solid #dddddd;
    box-shadow: rgba(60, 64, 67, 0.3) 0px 1px 2px 0px, rgba(60, 64, 67, 0.15) 0px 2px 6px 2px;
}
</style>