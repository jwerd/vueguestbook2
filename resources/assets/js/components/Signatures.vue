<template>
    <div>
        <div class="card mt-2" v-for="signature in signatures">
            <div class="card-header">
                <span class="glyphicon glyphicon-user" id="start"></span>
                <label id="started">By</label> {{ signature.name }}
            </div>
            <div class="card-body">
                <div class="col-md-2">
                    <div class="thumbnail">
                        <img :src="signature.avatar" :alt="signature.name">
                    </div>
                </div>
                <p>{{ signature.body }}</p>
            </div>
            <div class="card-footer">
                <span class="glyphicon glyphicon-calendar" id="visit"></span> {{ signature.date }} |
                <span class="glyphicon glyphicon-flag" id="comment"></span>
                <a href="#" id="comments" @click="report(signature.id)">Report</a>
            </div>
        </div>
        <paginate
                :page-count="pageCount"
                :click-handler="fetch"
                :prev-text="'Prev'"
                :next-text="'Next'"
                :next-class="'page-item'"
                :prev-class="'page-item'"
                :page-class="' page-item'"
                :page-link-class="'page-link'"
                :prev-link-class="'page-link'"
                :next-link-class="'page-link'"
                :container-class="'pagination mt-2'">
        </paginate>
    </div>
</template>

<script>
    export default {

        data() {
            return {
                signatures: [],
                pageCount: 1,
                endpoint: 'api/signatures?page='
            };
        },

        created() {
            this.fetch();
        },

        methods: {
            fetch(page = 1) {
                axios.get(this.endpoint + page)
                    .then(({data}) => {
                        this.signatures = data.data;
                        this.pageCount = data.meta.last_page;
                    });
            },

            report(id) {
                if(confirm('Are you sure you want to report this signature?')) {
                    axios.put('api/signatures/'+id+'/report')
                    .then(response => this.removeSignature(id));
                }
            },

            removeSignature(id) {
                this.signatures = _.remove(this.signatures, function (signature) {
                    return signature.id !== id;
                });
            }
        }
    }
</script>