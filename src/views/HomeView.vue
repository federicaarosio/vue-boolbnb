<script>
    import axios from 'axios';

    export default {
    name: "HomeView",
    data() {
        return {
            apartments: [],
        }
    },
    methods: {
        getApartments() {
            axios.get('http://127.0.0.1:8000/api/apartments', {
                params: {
                }
            })
            .then( response => {
                console.log(response.data.results);
                this.apartments = response.data.results;
            })
            .catch(function (error) {
                console.log(error);
            });
        },
        getRandomArbitrary(min, max) {
            return Math.random() * (max - min) + min;
        }
    },
    created() {
        this.getApartments();
    }
    }
</script>

<template>
    <div class="container-fluid mt-4 px-sm-2 px-md-4  px-xl-5 my_container">
        <div class="row justify-content-center ">
            <div class="col-11 col-md-6 col-lg-4 col-xl-3 col-xxl-2 mb-3" v-for="apartment in apartments">
                <div class="my-card">
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

.text-secondary {
    color: $secondary-color;
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
</style>
