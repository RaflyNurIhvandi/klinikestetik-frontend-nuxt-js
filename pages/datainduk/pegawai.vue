<template>
    <div>
        <h1>Data Pegawai</h1>

        <!-- form -->
        <v-form @submit.prevent="save" v-model="isvalid" ref="form">
            <v-container>
                <v-row>
                    <v-col cols="12" md="4">
                        <v-text-field ref="tap1" @keydown.enter="fild1" v-model="form.namapegawai" :rules="nameRules" label="Name" required></v-text-field>
                    </v-col>

                    <v-col cols="12" md="8">
                        <v-text-field ref="tap2" @keydown.enter="fild2" v-model="form.alamatpegawai" :rules="addressRules" label="Address" required></v-text-field>
                    </v-col>
                </v-row>

                <v-row>
                    <v-col cols="12" md="4">
                        <v-text-field ref="tap3" @keydown.enter="fild3" v-model="form.notelppegawai" :rules="nohpRules" label="Phone Number" @keypress="isNumber($event)" required></v-text-field>
                    </v-col>

                    <v-col cols="12" md="4">
                        <v-text-field ref="tap4" @keydown.enter="fild4" v-model="form.kotapegawai" :rules="cityRules" label="City" required></v-text-field>
                    </v-col>

                    <v-col cols="12" md="4">
                        <v-text-field ref="tap5" @keydown.enter="fild5" v-model="form.provinsipegawai" :rules="stateRules" label="State" required></v-text-field>
                    </v-col>
                </v-row>

                <v-row>
                    <v-col cols="12" md="4">
                        <v-select v-model="form.jabatanpegawai" :items="items" :rules="jabRules" label="Jabatan" required></v-select>
                    </v-col>

                    <v-col cols="12" md="4">
                        <v-text-field ref="tap7" @keydown.enter="fild7" v-model="form.password" :rules="passRules" label="Password" required></v-text-field>
                    </v-col>
                </v-row>
            </v-container>

            <!-- button -->
            <v-row>
            <v-col class="d-flex flex-row mb-6 bg-surface-variant mt-5" cols="12" ms="6" md="6">
                <v-btn class="mr-4" width="150px" color="success" @click="reset" type="reset" v-show="!updateSubmit">
                    New Employee
                </v-btn>

                <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" @click="save" v-show="!updateSubmit">
                    Save
                </v-btn>

                <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="updateSubmit" v-on:click="update(form)">
                    Save
                </v-btn>

                <v-btn color="error" width="150px" @click="reset" type="reset">
                    Cancel
                </v-btn>
            </v-col>

          <v-col class="text-end d-flex flex-row-reverse mb-6 bg-surface-variant mt-8" cols="12" ms="6" md="6">
                <a @click="getExcel()">| Export to Excel |</a>
                <a @click="getPdf()">&nbsp;Export to PDF&nbsp;</a>
                <a @click="getCetak()">| Print |</a>
          </v-col>
        </v-row>
            <!-- button -->
        </v-form>
        <!-- form -->

        <!-- table -->
        <template>
            <div>
                <v-text-field append-icon="mdi-magnify" v-model="cari" label="Search Employee's Name" single-line></v-text-field>
                <v-data-table :headers="headerkaryawan" :items="users" :items-per-page="5" class="elevation-1">
                    <template v-slot:[`item.tools`]="{ item }">
                        <!-- <v-btn @click="editkaryawan(item)"> -->
                            <v-icon small color="success" @click="editkaryawan(item)" style="border: solid thin green">
                                mdi-pencil
                            </v-icon>
                        <!-- </v-btn> -->
                        <!-- <v-btn @click="deletekaryawan(item)"> -->
                            <v-icon small color="error" @click="deletekaryawan(item)" style="border: solid thin red">
                                mdi-delete-empty
                            </v-icon>
                        <!-- </v-btn> -->
                    </template>
                </v-data-table>
            
            </div>
        </template>
        <!-- table -->
    </div>    
</template>

<!-- ===== -->

<script>
import axios from "axios";
import Swal from 'sweetalert2';

export default {
    data() {
        return {
            headerkaryawan: [
                { text: 'No', value: "id" },
                { text: 'Employee Id', value: "kodepegawai" },
                { text: 'Name', value: "namapegawai" },
                { text: 'Address', value: "alamatpegawai" },
                { text: 'City', value: "kotapegawai" },
                { text: 'Tools', value: "tools" },
            ],

            cari:"",
            form: {
                id: "",
                namapegawai: "",
                alamatpegawai: "",
                notelppegawai: "",
                kotapegawai: "",
                provinsipegawai: "",
                jabatanpegawai: "",
                password: ""
            },
            editkar: {
                id: "",
                namapegawai: "",
                alamatpegawai: "",
                notelppegawai: "",
                kotapegawai: "",
                provinsipegawai: "",
                emailpegawai: "",
                jabatanpegawai: "",
                password: ""
            },

            valid: false,
            users: [],
            updateSubmit: false,
            nameRules: [
                v => v != '' || 'Name is required',
            ],
            addressRules: [
                v => !!v || 'Address is required',
            ],
            nohpRules: [
                v => !!v || 'Phone Number is required',
            ],
            cityRules: [
                v => !!v || 'City is required',
            ],
            stateRules: [
                v => !!v || 'State is required',
            ],
            // jabRules: [
            //     v => !!v || 'Jabatan is required',
            // ],
            items: [
                'Dokter', 'Terapis', 'Asisten'
            ],
            passRules: [
                v => !!v || 'Password is required',
            ],
            isvalid: false,
        }
    },

    methods: {
        fild1(){
            this.$refs.tap2.focus()
        },
        fild2(){
            this.$refs.tap3.focus()
        },
        fild3(){
            this.$refs.tap4.focus()
        },
        fild4(){
            this.$refs.tap5.focus()
        },
        fild5(){
            this.$refs.tap7.focus()
        },
        // fild6(){
        //     this.$refs.tap7.focus()
        // },
        fild7(){
            this.$refs.tap1.focus()
        },
        reset(){
          this.$refs.form.reset()
        },
        
        load() {
            this.apiurl = process.env.API_URL
            axios.get(this.apiurl+'/pegawai')
                .then((response) => {
                    this.users = response.data.data
                    console.log(response.data)
                })
                .catch(() => {
                    console.log(err)
                });
            },

            save() {
                Swal.fire({
                    title: 'Pastikan data yang diisi benar',
                    text: "Anda Yakin?",
                    icon: 'info',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes'
                }).then((result) => {
                    if (result.isConfirmed) {
                this.apiurl = process.env.API_URL
                axios.post(this.apiurl+'/pegawai/store', this.form)
                    .then(() => {
                        this.load();
                        this.form.namapegawai = "";
                        this.form.alamatpegawai = "";
                        this.form.notelppegawai = "";
                        this.form.kotapegawai = "";
                        this.form.provinsipegawai = "";
                        this.form.jabatanpegawai = "";
                        this.form.password = "";
                        Swal.fire(
                            'Berhasil',
                            'Data berhasil disimpan',
                            'success'
                        )
                    })
                    let timerInterval
                    Swal.fire({
                        title: 'Menyimpan data',
                        html: 'Data akan tersimpan pada <b></b> milliseconds.',
                        timer: 1300,
                        timerProgressBar: true,
                        didOpen: () => {
                            Swal.showLoading()
                            const b = Swal.getHtmlContainer().querySelector('b')
                            timerInterval = setInterval(() => {
                            b.textContent = Swal.getTimerLeft()
                            }, 100)
                        },
                        willClose: () => {
                            clearInterval(timerInterval)
                        }
                        }).then((result) => {
                            if (result.dismiss === Swal.DismissReason.timer) {
                                console.log('I was closed by the timer')
                            }
                        })
                    }
                    })
                    .catch(() => {
                    Swal.fire({
                        icon: 'error',
                        title: 'Gagal',
                        text: 'Data gagal disimpan',
                    })
                })
            },

            editkaryawan(item) {
                this.updateSubmit = true;
                this.editkar = Object.assign({}, item);
                this.form.id = this.editkar.id;
                this.form.notelppegawai = this.editkar.notelppegawai;
                this.form.namapegawai = this.editkar.namapegawai;
                this.form.alamatpegawai = this.editkar.alamatpegawai;
                this.form.kotapegawai = this.editkar.kotapegawai;
                this.form.provinsipegawai = this.editkar.provinsipegawai;
                this.form.jabatanpegawai = this.editkar.jabatanpegawai;
                this.form.password = this.editkar.password;
            },

            update(form) {
                Swal.fire({
                    title: 'Do you want to save the changes?',
                    showDenyButton: true,
                    showCancelButton: true,
                    confirmButtonText: 'Save',
                    denyButtonText: `Don't save`,
                }).then((result) => {
                    if (result.isConfirmed){
                this.apiurl = process.env.API_URL
                axios.put(this.apiurl+`/pegawai/update/${form.id}`, {
                        'namapegawai': this.form.namapegawai,
                        'alamatpegawai': this.form.alamatpegawai,
                        'notelppegawai': this.form.notelppegawai,
                        'kotapegawai': this.form.kotapegawai,
                        'provinsipegawai': this.form.provinsipegawai,
                        'jabatanpegawai': this.form.jabatanpegawai,
                        'password': this.form.password,
                    })
                    .then(() => {
                        this.load();
                        this.form.namapegawai = "";
                        this.form.alamatpegawai = "";
                        this.form.notelppegawai = "";
                        this.form.kotapegawai = "";
                        this.form.provinsipegawai = "";
                        this.form.jabatanpegawai = "";
                        this.form.password = ""
                        this.updateSubmit = false;
                    })
                        Swal.fire('Saved!', '', 'success')
                    } else if (result.isDenied) {
                        Swal.fire('Changes are not saved', '', 'info')
                    }
                })
                .catch(() => {
                    Swal.fire({
                        icon: 'error',
                        title: 'Gagal',
                        text: 'Data gagal disimpan',
                    })
                })    
            },

            deletekaryawan(item) {
                Swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                    }).then((result) => {
                    if (result.isConfirmed) {
                this.apiurl = process.env.API_URL
                axios.delete(this.apiurl+`/pegawai/delete/${item.kodepegawai}`)
                    .then(() => {
                        this.load();
                        this.form.namapegawai = "";
                        this.form.alamatpegawai = "";
                        this.form.notelppegawai = "";
                        this.form.kotapegawai = "";
                        this.form.provinsipegawai = "";
                        this.form.jabatanpegawai = "";
                        this.form.password = "";
                    })
                        Swal.fire(
                        'Deleted!',
                        'Your file has been deleted.',
                        'success'
                        )
                    }
                })
            },
            
            getCetak(){
                this.apiurl = process.env.API_URL
                let uri = this.apiurl+`/cetak`;
                window.open(uri, "_blank", "width=800,height=600,left=300,top=200");
            },

            getExcel(){
                this.apiurl = process.env.API_URL
                let uri = this.apiurl+`/excel`;
                window.open(uri, "_self", "width=800,height=600,left=300,top=200");
            },

            getPdf(){
                this.apiurl = process.env.API_URL
                let uri = this.apiurl+`/pdf`;
                window.open(uri, "_self", "width=800,height=600,left=300,top=200");
            },

            isNumber: function (evt) {
                evt = (evt) ? evt : window.event;
                var charCode = (evt.which) ? evt.which : evt.keyCode;
                if ((charCode > 31 && (charCode < 48 || charCode > 57)) && charCode !== 46) {
                    evt.preventDefault();;
                } else {
                    return true;
                }
            },
        },
            
        mounted() {
            // this.$refs.tap1.focus();
            this.load();
        },
}
</script>