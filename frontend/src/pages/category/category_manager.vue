<template>
    <div class="mdk-drawer-layout__content page">

        <div class="container-fluid page__container">
            <!-- <ol class="breadcrumb">
              <li class="breadcrumb-item"><router-link :to="{ name: 'home'}">Home</router-link></li>
              <li class="breadcrumb-item active">Category Course Manager</li>
            </ol> -->
            <div class="d-flex flex-column flex-sm-row flex-wrap mb-headings align-items-start align-items-sm-center">
                <div class="flex mb-2 mb-sm-0">
                    <h1 class="h2">Category Course Manager</h1>
                </div>
                <router-link :to="{ name: 'course_add_category' }" class="btn btn-success">
                    Add a Category
                </router-link>
            </div>
            <div class="card">
                <div class="card-body">
                    <div class="row">
                        <div class="col-lg-12">
                            <div class="table-responsive border-bottom" data-toggle="lists"
                                 data-lists-values='["js-lists-values-employee-name"]'>

                                <table class="table mb-0">
                                    <thead>
                                    <tr>

                                        <th style="width: 18px;">
                                            <div class="custom-control custom-checkbox">
                                                <input type="checkbox" class="custom-control-input js-toggle-check-all"
                                                       data-target="#staff" id="customCheckAll">
                                                <label class="custom-control-label" for="customCheckAll"><span
                                                        class="text-hide">Toggle all</span></label>
                                            </div>
                                        </th>

                                        <th>Category name</th>


                                        <th style="width: 200px;">Description</th>
                                        <th style="width: 24px;"></th>
                                    </tr>
                                    </thead>
                                    <tbody class="list" id="staff">

                                    <tr class="selected" v-for="category in category_list">

                                        <td>
                                            <div class="custom-control custom-checkbox">
                                                <input type="checkbox"
                                                       class="custom-control-input js-check-selected-row"
                                                       :id="'company_' + category.id">
                                                <label class="custom-control-label"
                                                       :for="'company_' + category.id"><span
                                                        class="text-hide">Check</span></label>
                                            </div>
                                        </td>

                                        <td>

                                            <div class="media align-items-center">
                                                <div class="media-body">
                                                    <span class="js-lists-values-employee-name">{{ category.name }}</span>
                                                </div>
                                            </div>

                                        </td>


                                        <td>
                                            <small class="text-muted">{{ category.description }}</small>
                                        </td>

                                        <td class="text-right">
                                            <a href="#" class="text-muted" data-toggle="dropdown">
                                                <i class="material-icons">more_vert</i>
                                            </a>
                                            <div class="dropdown-menu dropdown-menu-right">
                                                <router-link
                                                        :to="{ name: 'category_edit_course', params: {id:category.id} }"
                                                        class="dropdown-item">
                                                    Edit
                                                </router-link>
                                                <a href="#" @click.prevent="deleteCategory(category.id)"
                                                   class="dropdown-item">Delete</a>
                                            </div>
                                        </td>
                                    </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <ul class="pagination justify-content-center pagination-sm">
                <li
                        v-if="pagination.current_page>1"
                        class="page-item"
                >
                    <a
                            @click.prevent="getCategoryList(pagination.current_page - 1)"
                            class="page-link"
                            href="#"
                            aria-label="Previous"
                    >
            <span
                    aria-hidden="true"
                    class="material-icons"
            >chevron_left</span>
                        <span>Prev</span>
                    </a>
                </li>

                <li
                        :key="page"
                        v-for="page in pagesNumber"
                        :class="[page == pagination.current_page ? 'active' : null , 'page-item']"
                >
                    <a
                            href="#"
                            v-on:click.prevent="getCategoryList(page)"
                            class="page-link"
                            aria-label="Previous"
                    >{{ page }}</a>
                </li>

                <li v-if="pagination.current_page < pagination.last_page">
                    <a
                            href="javascript:void(0)"
                            aria-label="Next"
                            v-on:click.prevent="getCategoryList(pagination.current_page + 1)"
                    >
                        <span aria-hidden="true"></span>
                        <span
                                aria-hidden="true"
                                class="material-icons"
                        >chevron_right</span>
                    </a>
                </li>
            </ul>
        </div>

    </div>
</template>

<script>
    export default {
        data() {
            return {
                category_list: [],
                pagination: {
                    total: 0,
                    per_page: 2,
                    from: 1,
                    to: 0,
                    current_page: 1
                },
            };
        },
        computed: {
            pagesNumber() {
                if (!this.pagination.to) {
                    return [];
                }
                let from = this.pagination.current_page - this.offset;
                if (from < 1) {
                    from = 1;
                }

                let pagesArray = [];
                for (let page = 1; page <= this.pagination.last_page; page++) {
                    pagesArray.push(page);
                }
                return pagesArray;
            }
        },
        mounted() {
            this.getCategoryList(this.pagination.current_page);
            this.fetchCompanies();
        },
        methods: {
            getCategoryList(page) {
                this.$store.dispatch("enableLoading");
                axios
                    .get(this.routes.category.SHOW_CATEGORY + '/all/' + 10, {
                        params: {
                            page: page
                        }
                    })
                    .then(response => {
                        this.category_list = response.data.data
                        this.pagination.from = response.data.from;
                        this.pagination.to = response.data.to;
                        this.pagination.total = response.data.total;
                        this.pagination.per_page = response.data.per_page;
                        this.pagination.last_page = response.data.last_page;
                        this.pagination.current_page = response.data.current_page;
                        this.$store.dispatch("disableLoading");
                    }).catch(err => {
                    this.$store.dispatch("disableLoading");
                    console.log(err)
                });
            },
            fetchCompanies() {
                this.$store.dispatch("enableLoading");
                axios.get(this.routes.category.SHOW_CATEGORY + '/all/' + 10).then(res => {
                    this.category_list = res.data.data
                    this.$store.dispatch("disableLoading");
                }).catch(err => {
                    this.$store.dispatch("disableLoading");
                    console.log(err)
                });
            },
            deleteCategory(id) {
                var confirmed = confirm("Are you sure you want to delete this category?");
                if (confirmed) {
                    axios.get(this.routes.category.DELETE_CATEGORY + id).then(res => {
                        this.fetchCompanies();
                    }).catch(err => {
                        console.log(err.response);
                    });
                }
            }
        }
    };
</script>

<style>
</style>
