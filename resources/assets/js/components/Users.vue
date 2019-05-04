<template>
    <div class="container">
        <div class="row mt-5">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                    <h3 class="card-title">Users Table</h3>

                    <div class="card-tools">
                        <button class="btn btn-success" @click = "newModal">Add New <i class="fa fa-user-plus fa-fw"></i></button>
                    </div>

                </div>
                <!-- /.card-header -->
                <div class="card-body table-responsive p-0">
                <table class="table table-hover">
                    <tbody>
                    <tr>
                        <th>ID</th>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Type</th>
                        <th>Registered At</th>
                        <th>Modify</th>
                        </tr>
                    <tr v-for="user in users" :key="user.id">
                        <td>{{user.id}}</td>
                        <td>{{user.name}}</td>
                        <td>{{user.email}}</td>
                        <td>{{user.type | upText}}</td>
                        <td>{{user.created_at | myDate}}</td>
                        <td>
                            <a href="#" @click="editModal(user)"><i class="fa fa-edit blue"></i></a>
                            /
                            <a href="#" @click="deleteUser(user.id)"><i class="fa fa-trash red"></i></a>
                        </td>
                    </tr>
                </tbody></table>
                </div>
                <!-- /.card-body -->
            </div>
            <!-- /.card -->
            </div>
        </div>
        <!-- Modal -->
        <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">{{edit_mode ? "Edit User" : "Add User"}}</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <form @submit.prevent="edit_mode ? updateUser() : createUser()" >
                    <div class="modal-body">
                        <!-- Add User Form -->
                        <div class="form-group">
                            <input v-model="form.name" type="text" name="name"
                            placeholder="User Name"
                            class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                            <has-error :form="form" field="name"></has-error>
                        </div>
                        <div class="form-group">
                            <input v-model="form.email" type="email" name="email"
                            placeholder="Email"
                            class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                            <has-error :form="form" field="email"></has-error>
                        </div>
                        <div class="form-group">
                            <textarea v-model="form.bion" id="bio" name="bio"
                            placeholder="Short bio (optional)"
                            class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                            <has-error :form="form" field="bio"></has-error>
                        </div>
                        <div class="form-group">
                            <select v-model="form.type" id="type" name="type"
                            class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                <option value="">Select User Role</option>
                                <option value="admin">Admin</option>
                                <option value="user">Standart User</option>
                                <option value="author">Author</option>
                            </select>
                            <has-error :form="form" field="type"></has-error>
                        </div>
                        <div class="form-group">
                            <input v-model="form.password" type="password" name="password"
                            placeholder="Password"
                            class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                            <has-error :form="form" field="password"></has-error>
                        </div>
                        <!-- End Add User Form -->
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                        <button type="submit" class="btn btn-primary">{{edit_mode ? "Edit" : "Create"}}</button>
                    </div>
                    </form>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                edit_mode: false,
                users: {},
                form: new Form({
                    id : '',
                    name : '',
                    email : '',
                    password : '',
                    type : '',
                    bio : '',
                    photo : ''                   
                })
            }
        }, 
        methods: {
            updateUser(id){
                this.$Progress.start();
                console.log("Updating the User")
                this.form.put('api/user/'+this.form.id)
                    .then(()=>{
                        swal.fire(
                        'Uptaded!',
                        'User has been updated',
                        'success'
                        );
                        $('#addNew').modal('hide');
                        fire.$emit('refreshUserTable');
                        this.$Progress.finish();
                    })
                    .catch(()=>{
                        this.$Progress.fail();
                    });
            },
            editModal(user){
                this.edit_mode = true;
                this.form.reset();
                $('#addNew').modal('show');
                this.form.fill(user);
                console.log(this.edit_mode);
            },
            newModal(){
                this.edit_mode = false;
                this.form.reset();
                $('#addNew').modal('show');
                console.log(this.edit_mode);
            },
            
            deleteUser(id){
                swal.fire({
                title: 'Are you sure?',
                text: "You won't be able to revert this!",
                type: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                if (result.value) {
                    //Send Ajax request
                    this.form.delete('api/user/'+id)
                    .then(()=>{
                        swal.fire(
                        'Deleted!',
                        'Your file has been deleted.',
                        'success'
                        );
                        fire.$emit('refreshUserTable');
                    })
                    .catch(()=>{
                        swal.fire("Failed", "There is something fishy here", "warning");
                    });
                    
                }
                })
            },
            loadUsers(){
                axios.get('api/user').then(({data})=>this.users=data.data);
                console.log("--------------Fetched Users from database---------");
            },
            createUser(){
                console.log('Creating the User');
                this.$Progress.start();
                this.form.post('api/user')
                .then(()=>{
                    $('#addNew').modal('hide');
                    fire.$emit('refreshUserTable');
                    toast.fire({
                    type: 'success',
                    title: 'User created successfully'
                    });
                    this.$Progress.finish();
                    
                })
                .catch(()=>{
                   
                })
            }
        },
        created() {
            this.loadUsers();
            fire.$on('refreshUserTable', ()=>{this.loadUsers()});
            
            //setInterval(()=>this.loadUsers(), 30000);
        }
    }
</script>
