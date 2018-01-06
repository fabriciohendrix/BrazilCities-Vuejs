<template>
    <a class="button is-danger is-loading" v-show="isLoading">Loading</a>
    
    <div class="container">
        <h1 class="title">
            <strong>{{title}}</strong>
        </h1>
        <div class="columns">
            <div class="column is-5">
                <div class="field has-addons">
                    <div class="control is-expanded">
                        <input class="input is-danger" type="text" placeholder="Digite aqui sua busca" @keyup.enter.native="searchCities" v-model="search">
                    </div>
                    <div class="control">
                        <button class="button is-danger" @click.prevent="searchCities">Busca</button>
                    </div>
                </div>
 
                <div class="column is-12" v-show="searchTerm">
                    Sua busca pelo termo <strong>"{{ search }}"</strong> 
                    retornou <strong>{{ searchResultsNumber }}</strong> resultados.
                </div>
            </div> 

            <div class="column is-6"></div>

            <div class="column is-1">
                <a class="button is-expanded is-danger is-pulled-right" @click.prevent="newCities">Adicionar nova cidade</a>
            </div>
        </div>
        
        <div class="columns is-multiline">
            <div class="column is-3" v-for="city in cities">
                <div class="card">
                    <div class="card-content">
                        <p class="title">{{city.nome_municipio}}</p>
                        <p class="subtitle">{{city.estado}}</p>

                        <div class="control">
                            <div class="tags has-addons">
                                <span class="tag">UF: </span>
                                <span class="tag is-danger">{{city.uf}}</span>
                            </div>
                        </div>

                        <div class="control">
                            <div class="tags has-addons">
                                <span class="tag">IBGE Code: </span>
                                <span class="tag is-danger">{{city.ibge_code}}</span>
                            </div>
                        </div>
                    </div>
                    <footer class="card-footer">
                        <p class="card-footer-item">
                            <a href="#" class="icon has-text-danger">
                                <i class="fa fa-lg fa-map-marker"></i>
                            </a>
                        </p>
                        <p class="card-footer-item">
                            <a href="#" @click.prevent="editCity(city)">
                                <span class="icon has-text-danger">
                                    <i class="fa fa-lg fa-edit"></i>
                                </span>
                            </a>
                        </p>
                        <p class="card-footer-item">
                            <a href="#" class="has-text-danger" @click.prevent="removeCityModal(city)">
                                <i class="fa fa-lg fa-trash"></i>
                            </a>
                        </p>
                    </footer>
                </div>
            </div>            
        </div>
           
        <div class="columns">
            <div class="column is-12">                 
                <Pagination :total="total" :page="page" :itens-per-page="itensPerPage" :search-results-number="searchResultsNumber"  @change-page="onChangePage"></Pagination>
            </div>
        </div>
    </div>

    <div id="modalCity" class="modal" :class="{'is-active':showModal}">
        <div class="modal-background"></div>

        <div class="modal-card">
            <header class="modal-card-head">
                <p class="modal-card-title">City: <strong>{{selected.nome_municipio}} - {{selected.uf}}</strong></p>
                <button class="delete" @click.prevent="showModal=false"></button>
            </header>

            <section class="modal-card-body">
                <div class="columns">
                    <div class="column">
                        <label class="label">City</label>
                        <p class="control">
                            <input class="input" type="text" placeholder="Text input" v-model="selected.nome_municipio">
                        </p>
                    </div>
                    
                    <div class="column">
                        <label class="label">State</label>
                        <p class="control">
                            <input class="input" type="text" placeholder="Código" v-model="selected.estado">
                        </p>
                    </div>
                </div>
                
                <div class="columns">
                    <div class="column">
                        <label class="label">UF</label>
                        <p class="control">
                            <input class="input" type="text" placeholder="Text input" v-model="selected.uf">
                        </p>
                    </div>
                    
                    <div class="column">
                        <label class="label">IBGE Code</label>
                        <p class="control">
                            <input class="input" type="text" placeholder="Código" v-model="selected.ibge_code">
                        </p>
                    </div>
                </div>                
                
                <div class="columns">
                    <div class="column">
                        <label class="label">Latitude</label>
                        <p class="control">
                            <input class="input" type="text" placeholder="Text input" v-model="selected.latitude">
                        </p>
                    </div>
                    
                    <div class="column">
                        <label class="label">Longitude</label>
                        <p class="control">
                            <input class="input" type="text" placeholder="Código" v-model="selected.longitude">
                        </p>
                    </div>
                </div>    
            </section>

            <footer class="modal-card-foot">
                <a class="button is-danger" @click.prevent="saveCity">Salvar</a>
                <a class="button" @click.prevent="showModal=false">Cancelar</a>
            </footer>            
            <button class="modal-close is-large" aria-label="close" @click.prevent="showModal=false"></button>            
        </div>
    </div>

    <div id="modalAdded" class="modal" :class="{'is-active':showModalAdded}">
        <div class="modal-background"></div>

        <div class="modal-card">
            <section class="modal-card-body">
                <h2 class="subtitle">
                    <strong>Item adicionado com sucesso!</strong>
                    <button class="delete is-pulled-right" @click.prevent="showModalAdded=false"></button>
                </h2>
            </section>   
            <button class="modal-close is-large" aria-label="close" @click.prevent="showModalAdded=false"></button>
        </div>
    </div>     

    <div id="modalRemove" class="modal" :class="{'is-active':showModalRemove}">
        <div class="modal-background"></div>

        <div class="modal-card">
            <section class="modal-card-body">
                <h2 class="subtitle">
                    Tem certeza que deseja remover: <strong>{{selected.nome_municipio}} - {{selected.uf}}</strong> ?
                </h2>
            </section>

            <footer class="modal-card-foot">
                <a class="button is-danger" @click.prevent="removeCity(city)">Sim, pode remover!</a>
                <a class="button" @click.prevent="showModalRemove=false">Cancelar</a>
            </footer>     
            <button class="modal-close is-large" aria-label="close" @click.prevent="showModalRemove=false"></button>
        </div>
    </div>

    <div id="modalRemoved" class="modal" :class="{'is-active':showModalRemoved}">
        <div class="modal-background"></div>

        <div class="modal-card">
            <section class="modal-card-body">
                <h2 class="subtitle">
                    <strong>Item removido com sucesso!</strong>
                    <button class="delete is-pulled-right" @click.prevent="showModalRemoved=false"></button>
                </h2>
            </section>   
            <button class="modal-close is-large" aria-label="close" @click.prevent="showModalRemoved=false"></button>
        </div>
    </div>    

    <div></div> 
</template>

<script>
import Pagination from "./Pagination.vue";

export default {
    data() {
        return {
            isLoading: false,
            title: "Brazil Cities",
            search: "",
            cities: [],
            page: 1,
            total: 0,
            selected: {},
            itensPerPage: 8,
            showModal: false,
            showModalAdded: false,
            showModalRemove: false,
            showModalRemoved: false,
            searchTerm: false,
            searchResultsNumber: 0,
            showPagination: true
        };
    },
    components: {
        Pagination
    },
    methods: {
        onChangePage(page) {
            this.page = page;
            this.loadCities();
        },
        showLoading() {
            this.isLoading = true;
        },
        hideLoading() {
            this.isLoading = false;
        },
        loadCities() {
            let t = this;
            this.showLoading();

            let start = this.page * this.itensPerPage - this.itensPerPage;
            let end = this.page * this.itensPerPage;

            let qString = "";
            if (this.search) {
                qString = `&q=${this.search}`;
                t.search = this.search;
            }

            this.$http.get(`/cities`).then(response => {
                t.citiesTotal = response.json();
                t.total = this.citiesTotal.length;
            });

            this.$http
                .get(
                    `/cities?_sort=nome_municipio&_order=asc&_start=${start}&_end=${end}${qString}`
                )
                .then(
                    response => {
                        t.cities = response.json();
                        t.searchResultsNumber =
                            response.headers["x-total-count"];

                        if (t.searchResultsNumber <= 10) {
                            this.showPagination = false;
                        } else {
                            this.showPagination = true;
                        }
                    },
                    error => {
                        console.log(error);
                    }
                )
                .finally(function() {
                    t.hideLoading();
                });
        },
        searchCities() {
            this.loadCities();
            this.searchTerm = true;
        },
        newCities() {
            this.selected = {};
            this.showModal = true;
        },
        editCity(city) {
            this.selected = city;
            this.showModal = true;
        },
        removeCity(city) {
            this.$http.delete(`/cities/${this.selected.id}`).then(result => {
                console.log("City removed!");
                this.showModalRemove = false;
                this.showModalRemoved = true;
                this.loadCities();
            });
        },
        removeCityModal(city) {
            this.selected = city;
            this.showModalRemove = true;
        },
        removedCityModal(city) {
            this.showModalRemoved = false;
        },
        saveCity() {
            if (this.selected.id != null) {
                this.$http
                    .put(`/cities/${this.selected.id}`, this.selected)
                    .then(
                        response => {
                            this.$set("selected", {});
                            this.$set("showModal", false);
                        },
                        error => {
                            console.error(error);
                        }
                    )
                    .finally(this.loadCities());
            } else {
                this.$http
                    .post(`/cities`, this.selected)
                    .then(
                        response => {
                            this.$set("selected", {});
                            this.$set("showModal", false);
                            this.$set("showModalAdded", true);
                        },
                        error => {
                            console.error(error);
                        }
                    )
                    .finally(this.loadCities());
            }
        },
        addedCityModal(city) {
            this.showModalAdded = true;
        }
    },
    created() {
        this.loadCities();
    }
};
</script>

<style>
.card .title {
    font-size: 1.6rem;
    line-height: 1.4rem;
    height: 46px;
}
.card .card-content {
    padding: 1rem;
}
.is-loading {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 999;
    font-size: 150px !important;
    opacity: 0.7;
    filter: alpha(opacity=70);
}
</style>