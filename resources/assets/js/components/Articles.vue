<template>
    <div>
        <h2>Articles</h2>

        <form action="" class="mb-2" @submit-prevent="addArticle">
            <div class="form-group">
                <label for="title">Title</label>
                <input name="title" id="title" type="text" class="form-control" v-model="article.title">
            </div>
            <div class="form-group">
                <label for="body">Body</label>
                <textarea name="body" id="body" cols="30" rows="10" class="form-control" v-model="article.body"></textarea>
            </div>

            <button type="submit" class="btn btn-success btn-block">Save</button>
        </form>

        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item">
                    <a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a>
                </li>
                <li class="page-item disabled">
                    <a class="page-link text-dark" href="#">
                        Page {{ pagination.current_page }} of {{ pagination.last_page }}
                    </a>
                </li>
                <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item">
                    <a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a>
                </li>
            </ul>
        </nav>

        <div class="card card-body mb-2" v-for="article in articles" v-bind:key="article.id">
            <h3>{{ article.title }}</h3>
            <p>{{ article.body }}</p>

            <hr>

            <button class="btn btn-danger" @click="deleteArticle(article.id)">Delete</button>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                article_id: '',
                articles: [],
                article: {
                    id: '',
                    title: '',
                    body: ''
                },
                pagination: {},
                edit: false
            }
        },

        created() {
            this.fetchArticles();
        },

        methods: {
            fetchArticles(page_url) {
                let me = this;
                page_url = page_url || 'api/articles';

                axios.get(page_url)
                    .then(res => {
                        this.articles = res.data.data;
                        me.makePagination(res.data.meta, res.data.links)
                    })
                    .catch(error => {
                        console.log(error);
                    });
            },

            makePagination(meta, links) {
                let pagination = {
                    current_page: meta.current_page,
                    last_page: meta.last_page,
                    next_page_url: links.next,
                    prev_page_url: links.prev
                };

                this.pagination = pagination;
            },

            deleteArticle(id) {
                if(confirm("Are you sure you want to delete this?")) {
                    axios.delete(`api/articles/${id}`)
                        .then(res => {
                            alert("Article deleted successfully.");
                            this.fetchArticles();
                        })
                        .catch(err => {
                            console.log(err);
                        });
                }
            },

            addArticle() {
                // Do articles here
            }
        }
    }
</script>

<style scoped>

</style>