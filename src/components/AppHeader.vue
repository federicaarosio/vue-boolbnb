<script>
    import tt from '@tomtom-international/web-sdk-services';

    export default {
        name: "AppHeader",
        data() {
            return {
                address: '',
                results: []
            }
        },
        watch: {
            address(newAddress, oldAddress) {
                if(newAddress == '') {
                    this.results = [];
                }
            }
        },
        methods: {
            addAddressFilter() {
                this.$emit('addressFilter', this.address);
            },
            resetFilter() {
                if(this.address == '') {
                    this.$emit('addressFilter', '');
                }
            },
            search() {
                tt.services.fuzzySearch({
                key: "rBePA1fHaJT71C0Mp1YWQFMD9dcMym9E",
                query: this.address,
                limit: 5,
                countrySet: 'IT',
                language: 'it-IT',
                }).then((response) => {
                    // console.log(response);
                    this.results = response.results;
                });

                if(this.address == '') {
                    this.results = []
                }
            },
            setSearch(address) {
                this.address = address;
                this.results = [];
                this.addAddressFilter();
            }
        }
    }
</script>

<template>
    <nav class="navbar navbar-expand navbar-light bg-white shadow-sm py-3" :class=" this.$route.name == 'show' ? 'd-none d-md-block' : '' ">
        <div :class=" this.$route.name == 'show' ? 'container' : 'container-fluid px-md-5' ">

            <div class="col-4 d-none d-md-block">
                <RouterLink class="navbar-brand d-lg-none" to="/">
                    <img src="../assets/img/logoBoolBnB.png" height="60">
                </RouterLink>
    
                <RouterLink class="navbar-brand d-none d-lg-inline-block" to="/">
                    <img src="../assets/img/logoBoolBnB-desktop.png" height="60">
                </RouterLink>
            </div>

            <div class="col-10 col-md-4 d-flex justify-content-center ">
                <div class="navbar-nav d-lg-block " :class=" this.$route.name == 'show' ? 'd-lg-none' : '' ">
                    <div class="input-wrapper d-flex rounded-end-5 rounded-start-5 position-relative ">
                        <div class="input-container rounded-start-5 d-flex flex-column justify-content-center ">
                            <label class="">Dove</label>
                            <input type="text" v-model="address" @keyup.enter="resetFilter" @keyup=" address.length > 3 ? search() : '' " placeholder="Cerca destinazione">
                        </div>
                        <div class="button-wrapper d-flex justify-content-center align-items-center ">
                            <button class="search rounded-circle d-flex justify-content-center align-items-center ">
                                <img src="../assets/img/search.svg">
                            </button>
                        </div>
                        <div v-if="results != '' " class="position-absolute my-results overflow-hidden ">
                            <ul class="list-unstyled m-0">
                                <li v-for="result in results" @click="setSearch(result.address.freeformAddress)">
                                    <div v-if="result.entityType == 'Municipality'">
                                        {{ result.address.municipality }}
                                    </div>
                                    <div  v-else-if="result.entityType == 'MunicipalitySubdivision'">
                                        {{ result.address.municipality }},
                                        {{ result.address.municipalitySubdivision }}
                                    </div>
                                    <div v-else>
                                        {{ result.address.streetName }},
                                        {{ result.address.municipality ? result.address.municipality : result.address.countrySecondarySubdivision }}
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>

            <div class="col-2 col-md-4 d-flex justify-content-end">
                <ul class="navbar-nav">
                    <li class="nav-item dropdown">
                        <button type="button" class="btn border border-secondary rounded-start-5 rounded-end-5 d-flex py-2 border-opacity-50 mb-1" data-bs-toggle="dropdown" aria-expanded="false">
                            <div style="width: 15px;" class="me-2">
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><path d="M0 96C0 78.3 14.3 64 32 64H416c17.7 0 32 14.3 32 32s-14.3 32-32 32H32C14.3 128 0 113.7 0 96zM0 256c0-17.7 14.3-32 32-32H416c17.7 0 32 14.3 32 32s-14.3 32-32 32H32c-17.7 0-32-14.3-32-32zM448 416c0 17.7-14.3 32-32 32H32c-17.7 0-32-14.3-32-32s14.3-32 32-32H416c17.7 0 32 14.3 32 32z"/></svg>
                            </div>
                            <img src="https://e7.pngegg.com/pngimages/717/24/png-clipart-computer-icons-user-profile-user-account-avatar-heroes-silhouette-thumbnail.png" width="25">
                        </button>
                        <ul class="dropdown-menu dropdown-menu-end">
                            <li><a class="dropdown-item" href="http://127.0.0.1:8000/">Accedi</a></li>
                            <li><a class="dropdown-item" href="http://127.0.0.1:8000/register">Registrati</a></li>
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
</template>

<style lang="scss" scoped>
@use '../assets/scss/partials/variables' as *;

.input-wrapper {
    width: 500px;
    height: 60px;
    border: 1px solid #e7e7e7;
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;

    .input-container {
        width: 90%;
        
        label {
            padding: 0 25px;
            font-size: 12px;
            font-weight: 500;
        }

        input {
            width: 100%;
            border: none;
            border-radius: 25px;
            padding: 0 20px;
            margin-left: 5px;
            
            &:focus {
                outline: none;
            }

            &::placeholder {
                font-size: 15px;
            }
        }
    }

    .button-wrapper {
        width: 10%;
        padding-right: 3px;
        
        button {
            width: 40px;
            height: 40px;
            background-color: $primary-color;
            color: white;
            border: none;

            img {
                width: 15px;
                height: 15px;
            }
        }
    }

    .my-results {
        width: 500px;
        top: 70px;
        padding: 1rem 0;
        background-color: white;
        border-radius: 30px;
        box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
        z-index: 1;

        li {
            padding: 1.4rem;
            &:hover {
                background-color: #f7f7f7;
            }
        }
    }
}

@media screen and (max-width: 576px) {
    .input-wrapper {
        width: 300px;

        .input-container {
            width: 80%;
        }

        .button-wrapper {
            width: 20%;
        }
    }


    .my-results {
        width: 350px !important;
    }
}
</style>