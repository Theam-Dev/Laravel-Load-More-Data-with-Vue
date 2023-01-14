<template>
    <div>
        <table class="table table-bordered table-hover">
            <thead>
                <tr>
                    <th>Id</th>
                    <th>Title</th>
                    <th>Body</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in dataResponse" :key="item.id">
                    <td>{{ item.id }}</td>
                    <td>{{ item.title }}</td>
                    <td>{{ item.body }}</td>
                </tr>
            </tbody>
        </table>
        <section class="py-5 text-center">
            <div v-show="moreExists">
                <button class="btn btn-primary" @click="loadMoreData()">Load More</button>
            </div>
        </section>
    </div>
</template>
<script>
import axios from 'axios';
export default {
    data() {
        return {
            dataResponse: {},
            moreExists: false,
            nextPage: 1,
            page: 1
        }
    },
    created() {
        this.getResults(this.page);
    },
    methods: {
        async getResults(page) {
            var app = this;
            axios.get('/api/post/?page=' + app.page)
                .then(function (resp) {
                    if (resp.data.current_page < resp.data.last_page) {
                        app.moreExists = true;
                        app.nextPage = resp.data.current_page + 1;

                    } else {
                        app.moreExists = false;
                    }
                    app.dataResponse = resp.data.data;
                })
                .catch(function (resp) {
                    console.log(resp);
                });
        },

        async loadMoreData() {
            var app = this;
            await axios.get('/api/post/?page=' + app.nextPage)
                .then(function (resp) {

                    if (resp.data.current_page < resp.data.last_page) {
                        app.moreExists = true;
                        app.nextPage = resp.data.current_page + 1;
                    } else {
                        app.moreExists = false;
                    }
                    resp.data.data.forEach(data => {
                        app.dataResponse.push(data);
                    })
                })
                .catch(function (resp) {
                    console.log(resp);
                });
        }
    }
}
</script>