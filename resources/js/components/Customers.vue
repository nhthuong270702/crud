<template>
    <div class="container-fluid mt-4">
        <div class="row justify-content-center">
            <div class="col-md-9">
            <div class="card">
                <div class="card-header text-center"><h3>Manager Customer</h3></div>
                <!-- du lieu -->
                <div style="overflow-x:auto;">
                        <table class="table table-striped table-bordered text-center" style="background-color: white">
                            <thead>
                                <tr class="bg-grey">
                                    <th>Name</th>
                                    <th>Phone</th>
                                    <th>Address</th>
                                    <th>Email</th>
                                    <th>City</th>
                                    <th colspan="2">Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="customer in customers" v-bind:key="customer.id_customer">
                                    <td>{{ customer.name_customer }}</td>
                                    <td>{{ customer.phone_customer }}</td>
                                    <td>{{ customer.address_customer }}</td>
                                    <td>{{ customer.email_customer }}</td>
                                    <td>{{ customer.city_customer }}</td>
                                    <td>
                                        <button class="btn btn-danger" @click="deleteCustomers(customer.id_customer)">
                                            Delete
                                        </button>
                                    </td>
                                    <td>    
                                        <button class="btn btn-warning" @click="editCustomer(customer)">
                                            Edit
                                        </button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <!-- phan trang -->
                        <nav aria-label="Page navigation example" style="display: flex; align-items: center; justify-content: center;">
                        <ul class="pagination">
                            <li class="page-item" v-bind:class="[{disabled: !pagination.prev_page_url}]">
                            <a class="page-link" href="#" @click="fetchCustomer(pagination.prev_page_url)" aria-label="Previous">
                                <span aria-hidden="true">&laquo;</span>
                                <span class="sr-only">Previous</span>
                            </a>
                            </li>
                            <li class="page-item"><a class="page-link">{{pagination.current_page}} .. {{ pagination.last_page }}</a></li>
                            <li class="page-item" v-bind:class="[{disabled: !pagination.next_page_url}]">
                            <a class="page-link" href="#" @click="fetchCustomer(pagination.next_page_url)" aria-label="Next">
                                <span aria-hidden="true">&raquo;</span>
                                <span class="sr-only">Next</span>
                            </a>
                            </li>
                        </ul>
                        </nav>
                </div>
            </div>
            </div>
            <div class="col-md-3" style="padding: 30px">
                <!-- them va sua -->
                <h4 class="text-center">Create or Update</h4>
                <form @submit.prevent="addCustomer">
                    <div class="form-group">
                        <label for="">Name</label>
                        <input type="text" class="form-control" v-model="customer.name_customer" required>
                    </div>
                    <div class="form-group">
                        <label for="">Phone</label>
                        <input type="text" class="form-control" v-model="customer.phone_customer" required>
                    </div>
                    <div class="form-group">
                        <label for="">Address</label>
                        <input type="text" class="form-control" v-model="customer.address_customer" required>
                    </div>
                    <div class="form-group">
                        <label for="">Email</label>
                        <input type="text" class="form-control" v-model="customer.email_customer" required>
                    </div>
                    <div class="form-group">
                        <label for="">City</label>
                        <input type="text" class="form-control" v-model="customer.city_customer" required>
                    </div>
                    <button class="btn btn-primary" type="submit">Save</button>
                </form> 
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return {
                customers:[],

                customer:{
                    id_customer:'',
                    name_customer:'',
                    phone_customer:'',
                    address_customer:'',
                    email_customer:'',
                    city_customer:'',
                },
                id_customer:'',
                pagination:{},
                edit:false
            }
        },
        created(){
            this.fetchCustomer();
        },
        methods:{
            fetchCustomer(page_url){
                let vm = this;
                page_url= page_url || `api/customers`;
                fetch(page_url)
                .then(res => res.json())
                .then(res =>{
                    this.customers = res.data
                    vm.makePagination(res.meta, res.links)
                })
                .catch(error => console.log(error));
            },
            makePagination(meta, links){
                let pagination = {
                    current_page: meta.current_page,
                    last_page: meta.last_page,
                    next_page_url:links.next,
                    prev_page_url: links.prev,
                }
                this.pagination = pagination
            },
            deleteCustomers(customer){
                if(confirm('Bạn muốn xóa?')){
                    axios.delete(`api/customers/${customer}`)
                    .then(res => {
                        alert('Đã xóa');
                        this.fetchCustomer();
                    })
                    .catch(err => console.log(err));
                }
            },
            addCustomer(){
                if(this.edit === false){
                    //add
                    let formData = new FormData();

                    formData.append('name_customer', this.customer.name_customer);
                    formData.append('phone_customer', this.customer.phone_customer);
                    formData.append('address_customer', this.customer.address_customer);
                    formData.append('email_customer', this.customer.email_customer);
                    formData.append('city_customer', this.customer.city_customer);
                
                    axios.post('api/customers', formData)
                    .then(res => {
                        alert('Added!');
                        this.customer.name_customer = "";
                        this.customer.phone_customer = "";
                        this.customer.address_customer = "";
                        this.customer.email_customer = "";
                        this.customer.city_customer = "";
                        this.fetchCustomer();
                    })
                    .catch(err => console.log(err))
                }else{
                    //edit                
                    axios.put(`api/customers/${this.customer.id_customer}`, {
                        name_customer : this.customer.name_customer,
                        phone_customer : this.customer.phone_customer,
                        address_customer : this.customer.address_customer,
                        email_customer : this.customer.email_customer,
                        city_customer : this.customer.city_customer
                    })
                    .then(res => {
                        alert('Updated!');
                        this.customer.name_customer = "";
                        this.customer.phone_customer = "";
                        this.customer.address_customer = "";
                        this.customer.email_customer = "";
                        this.customer.city_customer = "";
                        this.fetchCustomer();
                    })
                    .catch(err => console.log(err))
                }
            },
            editCustomer(customer){
                this.edit = true;
                this.customer.id_customer = customer.id_customer;
                this.customer.name_customer = customer.name_customer;
                this.customer.phone_customer = customer.phone_customer;
                this.customer.address_customer = customer.address_customer;
                this.customer.email_customer = customer.email_customer;
                this.customer.city_customer = customer.city_customer;
            }
        }
    }
</script>
