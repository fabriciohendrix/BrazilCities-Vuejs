<template>
    <nav class="pagination is-rounded"> 
        <a class="pagination-previous button is-danger" v-show="showPreviousButton" @click="goPreviousPage()">Anterior</a>
        <a class="pagination-next button is-danger" v-show="showNextButton" @click="goNextPage()">Próximo</a>
        <ul class="pagination-list">
            <li><a class="pagination-link button is-danger is-outlined" v-show="showPreviousButton" @click="goFirstPage()">1</a></li>
            <li><span class="pagination-ellipsis" v-show="showPreviousButton">...</span></li>
            <li><a class="pagination-link button is-danger is-outlined" v-show="showPreviousButton" @click="goPage(page-1)">{{page-1}}</a></li>
            <li><span class="pagination-link button is-danger">{{page}}</span></li>
            <li><a class="pagination-link button is-danger is-outlined" v-show="showNextButton" @click="goPage(page+1)">{{page+1}}</a></li>
            <li><span class="pagination-ellipsis" v-show="showNextButton">...</span></li>
            <li><a class="pagination-link button is-danger is-outlined" v-show="showNextButton" @click="goLastPage()" >{{totalPages}}</a></li>
        </ul>
    </nav>
</template>
<script>
export default {
    props: ["total", "page", "itensPerPage", "searchResultsNumber"],
    computed: {
        totalPages: function() {
            return Math.ceil(this.searchResultsNumber / this.itensPerPage);
        },
        showNextButton: function() {
            return this.page != this.totalPages;
        },
        showPreviousButton: function() {
            return this.page != 1;
        }
    },
    methods: {
        goNextPage: function() {
            this.$emit("change-page", this.page + 1);
        },
        goPreviousPage: function() {
            this.$emit("change-page", this.page - 1);
        },
        goFirstPage: function() {
            this.$emit("change-page", 1);
        },
        goLastPage: function() {
            this.$emit("change-page", this.totalPages);
        },
        goPage: function(page) {
            this.$emit("change-page", page);
        }
    }
};
</script>