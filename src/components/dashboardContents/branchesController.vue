<template> 
    <v-container> 
        <v-card> 
            <v-container grid-list-md mb-0>
    
                <h2 class="text-md-center">
                    Data Cabang Bengkel "Sejahtera 9456"
                </h2> 
                
                <v-layout row wrap style="margin:10px"> 
                    <v-flex xs6> 
                        <v-btn 
                            depressed 
                            dark 
                            rounded style="text-transform: none !important;" 
                            color = "green accent-3" 
                            @click="dialog = true" > 
                            
                            <v-icon size="18" class="mr-2">mdi-pencil-plus</v-icon> 
                            
                            Tambah Cabang Bengkel
                        </v-btn> 
                    </v-flex> 
                    
                    <v-flex xs6 class="text-right"> 
                        <v-text-field 
                            v-model="keyword" 
                            append-icon="mdi-search" 
                            label="Search" 
                            hide-details >
                        </v-text-field> 
                    </v-flex> 
                </v-layout> 
                
                <v-data-table 
                    :headers="headers" 
                    :items="branchess" 
                    :search="keyword" 
                    :loading="load" > 
                    <template v-slot:body="{ items }"> 
                        <tbody> 
                            <tr v-for="(item,index) in items" :key="item.id"> 
                                <td>{{ index + 1 }}</td> 
                                <td>{{ item.name }}</td> 
                                <td>{{ item.address }}</td> 
                                <td>{{ item.phoneNumber }}</td>
                                <td>{{ item.created_at }}</td> 
                                <td class="text-center"> 
                                    <v-btn icon color="indigo" light @click="editHandler(item)" > 
                                        <v-icon>mdi-pencil</v-icon> 
                                    </v-btn> 
                                    
                                    <v-btn icon color="error" light @click="deleteData(item.id)" > 
                                        <v-icon>mdi-delete</v-icon> 
                                    </v-btn> 
                                </td> 
                            </tr> 
                        </tbody> 
                    </template> 
                </v-data-table> 
            </v-container>
        </v-card> 

            <v-dialog v-model="dialog" persistent max-width="600px"> 
                <v-card> 
                    <v-card-title> <span class="headline">Data Cabang Bengkel Baru</span> </v-card-title>
                    <v-card-text> 
                        <v-container> 
                            <v-row> 
                                <v-col cols="12"> 
                                    <v-text-field label="Nama Cabang*" v-model="form.name" required></v-text-field> 
                                </v-col> 
                                <v-col cols="12"> 
                                    <v-text-field label="Alamat Cabang*" v-model="form.address" required></v-text-field> 
                                </v-col> 
                                <v-col cols="12"> 
                                    <v-text-field label="Nomor Telepon*" v-model="form.phoneNumber" required></v-text-field> 
                                </v-col> 
                            </v-row> 
                        </v-container> 
                        
                        <small>*indicates required field</small> 
                    </v-card-text> 
                    
                    <v-card-actions> 
                        <v-spacer></v-spacer> 
                            <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn> 
                            <v-btn color="blue darken-1" text @click="setForm()">Save</v-btn>
                    </v-card-actions> 
                </v-card> 
            </v-dialog> 
            
            <v-snackbar 
                v-model="snackbar"
                :color="color" 
                :multi-line="true" 
                :timeout="3000" > {{ text }} 
                
                <v-btn dark text @click="snackbar = false" > Close </v-btn> 
            </v-snackbar> 
            
    </v-container> 
</template> 
                    
<script> 
    export default { 
        data () { 
            return { 
                dialog: false, 
                keyword: '', 
                headers: [ 
                    { text: 'No', value: 'no' }, 
                    { text: 'Nama Cabang', value: 'name' }, 
                    { text: 'Alamat Cabang', value: 'address' }, 
                    { text: 'Nomor Telepon', value: 'phoneNumber' },
                    { text: 'Diinput Pada :', value: 'created_at'},
                    { text: 'Aksi', value: null }, 
                ], 
                branchess : [], 
                snackbar: false, 
                color: null, 
                text: '', 
                load: false,
                form: { 
                    name : '', 
                    address : '', 
                    phoneNumber : '',
                    created_at : '' 
                }, 
                branches : new FormData, 
                typeInput: 'new', 
                errors : '', 
                updatedId : '', 
            } 
        }, 
                
                
        methods:{ 
            getData(){ 
                var uri = this.$apiUrl + '/branches' 
                this.$http.get(uri).then(response =>{ 
                    this.branchess=response.data.message 
                }) 
            }, 
            
            sendData(){ 
                this.branches.append('name', this.form.name); 
                this.branches.append('address', this.form.address); 
                this.branches.append('phoneNumber', this.form.phoneNumber); 
                var uri =this.$apiUrl + '/branches' 
                this.load = true;
                this.$http.post(uri,this.branches).then(response =>{ 
                    this.snackbar = true; //mengaktifkan snackbar 
                    this.color = 'green'; //memberi warna snackbar 
                    this.text = response.data.message; //memasukkan pesan ke snackbar 
                    this.load = false; 
                    this.dialog = false; 
                    this.getData(); //mengambil data cabang
                    this.resetForm(); 
                }).catch(error =>{ 
                    this.errors = error;
                    this.snackbar = true; 
                    this.text = 'Try Again'; 
                    this.color = 'red'; 
                    this.load = false; }) 
            }, 
            
            updateData(){ 
                this.branches.append('name', this.form.name); 
                this.branches.append('address', this.form.address); 
                this.branches.append('phoneNumber', this.form.phoneNumber); 
                var uri = this.$apiUrl + '/branches/' + this.updatedId; 
                this.load = true;
                this.$http.post(uri,this.branches).then(response =>{
                    this.snackbar = true; //mengaktifkan snackbar 
                    this.color = 'green'; //memberi warna snackbar 
                    this.text = response.data.message; //memasukkan pesan ke snackbar 
                    this.load = false; 
                    this.dialog = false;
                    this.getData(); //mengambil data branches
                    this.resetForm(); 
                    this.typeInput = 'new'; 
                }).catch(error =>{ 
                    this.errors = error 
                    this.snackbar = true; 
                    this.text = 'Try Again'; 
                    this.color = 'red'; 
                    this.load = false; 
                    this.typeInput = 'new';
                }) 
            }, 
            
            editHandler(item){ 
                this.typeInput = 'edit'; 
                this.dialog = true; 
                this.form.name = item.name; 
                this.form.address = item.address; 
                this.form.phoneNumber = '', 
                this.updatedId = item.id;
            }, 
            
            deleteData(deleteId){ 
                //menghapus data
                var uri = this.$apiUrl + '/branches/' + deleteId; //data dihapus berdasarkan id
                this.$http.delete(uri).then(response =>{
                    this.snackbar = true;
                    this.text = response.data.message;
                    this.color = 'green'
                    this.deleteDialog = false;
                    this.getData();
                }).catch(error =>{
                    this.errors = error
                    this.snackbar = true;
                    this.text = error;
                    this.color = 'red';
                })
            },
            
            setForm(){ 
                if (this.typeInput === 'new') { 
                    this.sendData() 
                } else { 
                    console.log("dddd")
                    this.updateData() 
                } 
            }, 
            
            resetForm(){ 
                this.form = { 
                    name : '', 
                    address : '', 
                    phoneNumber : '' 
                } 
            } 
        }, 
            
        mounted(){ 
            this.getData(); 
        }, 
    } 
</script>