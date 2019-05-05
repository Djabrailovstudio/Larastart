


<template>
    <div class="container">
        <div class="row">
            <div class="col-md-12">
                <div class="card card-widget widget-user mt-3">
              <!-- Add the bg color to the header using any of the bg-* classes -->
                    <div class="widget-user-header text-white" style="background-image: url(./img/bog.jpg);">
                        <h3 class="widget-user-username">Elizabeth Pierce</h3>
                        <h5 class="widget-user-desc">Web Designer</h5>
                    </div>
                    <div class="widget-user-image">
                        <img class="img-circle" v-bind:src="getProfilePhoto()" alt="User Avatar">
                    </div>
                    <div class="card-footer">
                        <div class="row">
                        <div class="col-sm-4 border-right">
                            <div class="description-block">
                            <h5 class="description-header">3,200</h5>
                            <span class="description-text">SALES</span>
                            </div>
                            <!-- /.description-block -->
                        </div>
                        <!-- /.col -->
                        <div class="col-sm-4 border-right">
                            <div class="description-block">
                            <h5 class="description-header">13,000</h5>
                            <span class="description-text">FOLLOWERS</span>
                            </div>
                            <!-- /.description-block -->
                        </div>
                        <!-- /.col -->
                        <div class="col-sm-4">
                            <div class="description-block">
                            <h5 class="description-header">35</h5>
                            <span class="description-text">PRODUCTS</span>
                            </div>
                            <!-- /.description-block -->
                        </div>
                        <!-- /.col -->
                        </div>
                        <!-- /.row -->
                    </div>
                </div>

                <!-- end of card header -->

                <hr>


                <div class="card">
                    <div class="card-header p-2">
                        <ul class="nav nav-pills">
                            <li class="nav-item"><a class="nav-link active show" href="#activity" data-toggle="tab">Activity</a></li>
                            <li class="nav-item"><a class="nav-link" href="#settings" data-toggle="tab">Settings</a></li>
                        </ul>
                    </div><!-- /.card-header -->
                    <div class="card-body">
                    <div class="tab-content">
                        <div class="tab-pane active show" id="activity">
                            <h1>Show User Activity</h1>
                        </div>
                    <!-- /.tab-pane -->
                    

                    <div class="tab-pane" id="settings">
                        <form class="form-horizontal" enctype="multipart/form-data">
                        <div class="form-group">
                            <label for="inputName" class="col-sm-2 control-label">Name</label>

                            <div class="col-sm-10">
                            <input  v-model="form.name"  type="text" class="form-control" id="inputName" placeholder="Name">
                            </div>
                        </div>
                        <div class="form-group">
                            <label for="inputEmail" class="col-sm-2 control-label">Email</label>

                            <div class="col-sm-10">
                            <input v-model="form.email"  type="email" name="email" class="form-control" id="inputEmail" :class="{ 'is-invalid': form.errors.has('email') }"  placeholder="Email">
                            <has-error :form="form" field="email"></has-error>
                            </div>
                        </div>
                        
                        <div class="form-group">
                            <label for="inputExperience" class="col-sm-2 control-label">Experience</label>

                            <div class="col-sm-10">
                            <textarea class="form-control" id="inputExperience" placeholder="Experience"></textarea>
                            </div>
                        </div>
                        <div class="form-group">
                            <label for="file" class="col-sm-2 control-label">Upload Photo</label>
                            <div class="col-sm-10">
                                <input @change="updateProfile" type="file" id="file" class="form-controll" >
                            </div>    
                        </div>
                        <div class="form-group">
                            <label for="inputPassword" class="col-sm-2 control-label">Password</label>

                            <div class="col-sm-10">
                            <input v-model="form.password" :class="{ 'is-invalid': form.errors.has('password') }"  type="password" class="form-control" id="inputPassword" placeholder="Password">
                            <has-error :form="form" field="password"></has-error>
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="col-sm-offset-2 col-sm-10">
                            <button @click.prevent="updateInfo" type="submit" class="btn btn-danger">Submit</button>
                            </div>
                        </div>
                        </form>
                    </div>
                    <!-- /.tab-pane -->
                    </div>
                <!-- /.tab-content -->
              </div><!-- /.card-body -->
            </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                form: [],
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
        mounted() {
            console.log('Component mounted.')
        },
        methods: {
            getProfilePhoto(){
                return "img/profile/"+ this.form.photo
            },
            updateInfo(){
                this.$Progress.start();
                this.form.put('api/profile/')
                .then(()=>{
                    axios.get('api/profile')
                    .then(({data}) => (this.form.fill(data)));       
                    this.$Progress.finish();
                })
                .catch(()=>{
                    this.$Progress.fail();
                })
            },
            updateProfile(e){
                console.log('Uploading image');
                let file = e.target.files[0];
                console.log(file);
                let reader = new FileReader();
                if(file['size'] < 2111775){
                    reader.onloadend = (file)=>{
                        this.form.photo = reader.result;
                    }
                    reader.readAsDataURL(file);

                } else {
                    swal.fire({
                        type: 'error',
                        title: 'Oops',
                        text: 'You re uploading a large file'
                    });
                }
                
            }
        },
        created(){
            axios.get('api/profile')
            .then(({data}) => (this.form.fill(data)));
        }
    }
</script>
