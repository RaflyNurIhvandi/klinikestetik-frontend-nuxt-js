<template>
    <div>
        <h1>Pernyataan Kesediaan Terapis/Treatment</h1>

        <v-form @submit.prevent="save" v-model="isvalid" ref="form" enctype="multipart/form-data">
            <v-container>
                <v-row>
                    <v-col cols="12" md="4">
                        <v-text-field ref="tap1" @keydown.enter="fild1" v-model="form.nama" label="Customers" :rules="customerRules" required></v-text-field>
                    </v-col>
                    <v-col cols="12" md="4">
                    </v-col>
                    <v-col cols="12" md="4">
                        <v-text-field ref="tap2" @keydown.enter="fild2" v-model="form.tanggal" label="Date" :rules="dateRules" type="date" required></v-text-field>
                    </v-col>
                </v-row>
                <v-row>
                    <v-col cols="12" md="4">
                        <h3>Endro Andriyanto</h3>
                        <p>Jl. Watudamar 17, Girimoyo, Karangploso<br>
                        Malang, Telp. 085 855 566 467</p>
                    </v-col>
                    <v-col cols="12" md="4">
                    </v-col>
                    <v-col cols="12" md="4" ref="image">
                        <v-input onchange="tampilFoto()" @change="onFilePikced" accept="image/jpeg, image/jpg, image/png" prepend-icon="mdi-camera" type="file" id="file" name="file" v-model="form.filepernyataan" label="Foto Berkas Pernyataan" :rules="fotoRules" ref="tap3" @keydown.enter="fild3" required></v-input>
                    </v-col>
                </v-row>

                <v-col class="text-end d-flex flex-row-reverse mb-6 bg-surface-variant mt-1" cols="12" md="10">
                    <v-btn class="mr-4" color="primary" width="150px" @click="save">
                        Upload
                    </v-btn>
                </v-col>
            </v-container>
        </v-form>
    </div>
</template>

<script>
import axios from "axios";
import Swal from 'sweetalert2';

    export default{
        data() {
            return {
                // data: () => ({
                //     imageUrl: '',
                //     imageFile: null,
                //     filepernyataan: ''
                // }),

                form: {
                    id: "",
                    nama: "",
                    tanggal: "",
                    filepernyataan: ""
                },

                valid: false,
                pernyataans: [],
                customerRules: [
                    v => v != '' || 'Customers is required'
                ],
                dateRules: [
                    v => v != '' || 'Date is required'
                ],
                fotoRules: [
                    v => v != '' || 'Files cannot be empty'
                ],
                isvalid: false,
            }
        },

        methods: {
            // pickFile(){
            //     this.$refs.image.click()
            // },
            // onFilePicked(e){
            //     const files = e.target.files
            //     if(files[0] !== undefined){
            //         this.filepernyataan = files[0].name 
            //         if(this.filepernyataan.lastIndexOf('.')<= 0){
            //             return
            //         }
            //         const fr = new FileReader()
            //         fr.readAsDataURL(files[0])
            //         fr.addEventListener('load', () => {
            //             this.imageUrl = fr.result
            //             this.imageFile = files[0]
            //         })
            //     } else {
            //         this.filepernyataan = ''
            //         this.imageFile = ''
            //         this.imageUrl = ''
            //     }
            // },

            // fild1(){
            //     this.$refs.tap2.focus()
            // },
            // fild2(){
            //     this.$refs.tap3.focus()
            // },

            // save(){
            //     let formData = new FormData()
            //     formData.append('image_file', this.form.imageFile)

            //     axios.post(this.apiurl+'/pernyataan/store', formData)
            //     .then(res => {})
            //     .catch(err => {})
            // },

            load() {
                this.apiurl = process.env.API_URL
                axios.get(this.apiurl+'/pernyataan')
                .then((response) => {
                    this.pernyataans = response.data.data
                    console.log(response.data)
                })
                .catch(() => {
                    console.log(err)
                })
            },

            save() {
                this.apiurl = process.env.API_URL
                axios.post(this.apiurl+'/pernyataan/store', this.form)
                .then(() => {
                    this.load()
                    this.form.nama= ""
                    this.form.tanggal= ""
                    this.form.filepernyataan= ""
                    Swal.fire({
                        icon: 'success',
                        title: 'Your work has been saved',
                        showConfirmButton: true,
                    })
                    .catch(() => {
                        Swal.fire({
                            icon: 'error',
                            title: 'Gagal',
                            text: 'Data gagal disimpan',
                        })
                    })
                })
            },
            tampilFoto()
        {
            var fReader = new FileReader();
            fReader.readAsDataURL(document.getElementById("file").files[0]);
            fReader.onload = function (e)
            {
                document.getElementById("previewfoto").src = e.target.result;
            };
        }
        },

        mounted() {
            this.load();
        }
    }
</script>