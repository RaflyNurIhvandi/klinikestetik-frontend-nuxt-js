<template>
  <div>
    <v-container>
      <h1>Data Suplier</h1>
      <v-form @submit.prevent="save" v-model="isvalid" ref="form">
      <v-row>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.companyname" id="companyname" @keydown.enter="companyname" label="Company Name"></v-text-field>
        </v-col>
        <v-col cols="12" md="8">
          <v-text-field v-model="form.address" id="address" @keydown.enter="address" label="Address"></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.phonenumber" id="phonenumber" @keydown.enter="phonenumber" label="Phone Number"></v-text-field>
        </v-col>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.city" id="city" @keydown.enter="city" label="City"></v-text-field>
        </v-col>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.state" id="state" @keydown.enter="state" label="State"></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.contactperson" id="contactperson" @keydown.enter="contactperson" label="Contact Person"></v-text-field>
        </v-col>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.contactpersonphone" id="contactpersonphone"  @keypress="isNumber($event)" label="Contact Person Phone"></v-text-field>
        </v-col>
      </v-row>
    </v-form>
      <v-row>
        <v-col class="d-flex flex-row mb-6 bg-surface-variant mt-5" cols="12" ms="6" md="6">
          <v-btn class="mr-4" color="success" width="150px" @click="data">New Supplier</v-btn>
          <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="!updateSubmit" @click="save">Save</v-btn>
          <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="updateSubmit" v-on:click="update(form)">Save</v-btn>
          <v-btn color="error" class="mr-4" width="150px" @click="cencel">cencel</v-btn>
        </v-col>
        <v-col class="text-end d-flex flex-row-reverse mb-6 bg-surface-variant mt-7" cols="12" ms="6" md="6">
            <a @click="getPdf()">|&nbsp; Export to PDF</a>
            <a @click="getExcel()">Export to Excel&nbsp;&nbsp;</a>
            <a @click="getCetak()">Print &nbsp;|&nbsp;&nbsp;</a>
          </v-col>
      </v-row>
    </v-container>
    <div>
      <v-text-field v-model="cari" append-icon="mdi-magnify" label="Pencarian" single-line></v-text-field>
      <v-data-table :headers="headersupplier" :items="dtsuplier" :items-per-page="5" :search="cari" class="elevation-1">
        <template v-slot:[`item.aksi`]="{ item }">
          <v-icon color="success" @click="edit(item)">
            mdi-pencil
          </v-icon>
          <v-icon color="error" @click="hapus(item)">
            mdi-delete
          </v-icon>
        </template>
      </v-data-table>
    </div>
    <!-- {{ dtsuplier }} -->
  </div>
</template>
<script>
 import axios from 'axios';
 import Swal from "sweetalert2";
    export default {
      data() {
        return {
          form:{
            id:"",
            companyname:"",
            address:"",
            phonenumber:"",
            city:"",
            state:"",
            contactperson:"",
            contactpersonphone:""
          },
          sp:{
                id:"",
                namapemasok:"",
                alamatpemasok:"",
                namakontak:"",
                kotapemasok:"",
                provinsi:"",
                notelpkontak:"",
                notelppemasok:"",
          },
          headersupplier: [
        { text: 'No', value: 'id' },
        { text: 'CompanyName', value: 'namapemasok' },
        { text: 'Address', value: 'alamatpemasok' },
        { text: 'City', value: 'kotapemasok' },
        { text: 'ContactPerson', value: 'notelppemasok' },
        { text: 'Tools', value: 'aksi' },
      ],
          cari:"",
          dtsuplier:[],
          updateSubmit:false,
          isvalid: false,
        }
      },
     computed: {
      
     },
     methods:{
      getCetak() {
      let uri = this.apiurl + '/pemasok/print'
      window.open(uri, '_blank', 'width=800,height=600,left=300,top=200')
    },
    getExcel() {
      let uri = this.apiurl + '/pemasok/excel'
      window.open(uri, '_self', 'width=800,height=600,left=300,top=200')
    },
    getPdf() {
      let uri = this.apiurl + '/pemasok/pdf'
      window.open(uri, '_self', 'width=800,height=600,left=300,top=200')
    },
      hapus(item) {
        Swal.fire({
          title: 'Pastikan Data Yang Dihapus Benar',
          text: 'anda yakin?',
          showCancelButton: true,
          confirmButtonText: 'Yes',
        }).then((result) => {
          if (result.isConfirmed) {
        this.apiurl = process.env.API_URL
        axios
          .delete(this.apiurl +`/pemasoks/delete/${item.id}`)
          .then(()=>{
                this.load()
                this.$refs.form.reset()
                Swal.fire({
                  position: 'top-end',
                  icon: 'success',
                  title: 'Berasil Menghapus Data',
                  showConfirmButton: false,
                  timer: 1000
                })
                this.updateSubmit= false
              })
            }
        })
      },
      cencel(){
        this.$refs.form.reset()
      },
      update(form) {
        Swal.fire({
          title: 'Pastikan data yang diisi benar',
          text: 'anda yakin?',
          showCancelButton: true,
          confirmButtonText: 'Yes',
        }).then((result) => {
          if (result.isConfirmed) {
        this.apiurl = process.env.API_URL
        axios
          .put(this.apiurl + '/pemasoks/update/' + form.id, {
            'companyname': this.form.companyname,
            'address': this.form.address,
            'phonenumber': this.form.phonenumber,
            'city': this.form.city,
            'state': this.form.state,
            'contactperson': this.form.contactperson,
            'contactpersonphone': this.form.contactpersonphone,
          })
          .then(()=>{
                this.load()
                this.$refs.form.reset()
                Swal.fire({
                  position: 'top-end',
                  icon: 'success',
                  title: 'Berasil Menyimpan Data',
                  showConfirmButton: false,
                  timer: 1000
                })
                this.updateSubmit= false
              })
           }
        })
      },
      edit(item) {
        this.updateSubmit = true;
        this.sp = Object.assign({}, item);
        this.form.id = this.sp.id;
        this.form.companyname = this.sp.namapemasok;
        this.form.address = this.sp.alamatpemasok;
        this.form.phonenumber = this.sp.namakontak;
        this.form.city = this.sp.kotapemasok;
        this.form.state = this.sp.provinsi;
        this.form.contactperson = this.sp.notelpkontak;
        this.form.contactpersonphone = this.sp.notelppemasok;
      },

      save(){
        if(this.form.companyname == null){
          Swal.fire({
              icon: 'error',
              title: 'Data Tidak Boleh Kosong',
              showConfirmButton: false,
              timer: 1000
            })
            document.getElementById('companyname').focus()
        }else if(this.form.phonenumber == null){
          Swal.fire({
              icon: 'error',
              title: 'Data Tidak Boleh Kosong',
              showConfirmButton: false,
              timer: 1000
            })
            document.getElementById('phonenumber').focus()
          }else if(this.form.contactperson == null){
          Swal.fire({
              icon: 'error',
              title: 'Data Tidak Boleh Kosong',
              showConfirmButton: false,
              timer: 1000
            })
            document.getElementById('contactperson').focus()
          }else if(this.form.contactpersonphone == null){
          Swal.fire({
              icon: 'error',
              title: 'Data Tidak Boleh Kosong',
              showConfirmButton: false,
              timer: 1000
            })
            document.getElementById('contactpersonphone').focus()
          }else {
        Swal.fire({
          title: 'Pastikan data yang diisi benar',
          text: 'anda yakin?',
          showCancelButton: true,
          confirmButtonText: 'Yes',
        }).then((result) => {
          if (result.isConfirmed) {
            this.apiurl = process.env.API_URL
              axios
              .post(this.apiurl + '/pemasoks/save' ,this.form)
              .then(()=>{
                this.load()
                this.$refs.form.reset()
                Swal.fire({
                  position: 'top-end',
                  icon: 'success',
                  title: 'Berasil Menyimpan Data',
                  showConfirmButton: false,
                  timer: 1000
                })
              })
          }
        })
      }
      },
      data() {
        this.updateSubmit = false
        document.getElementById('companyname').focus()
        this.$refs.form.reset()
      },
      companyname(){
        document.getElementById('address').focus()
      },
      address(){
        document.getElementById('phonenumber').focus()
      },
      phonenumber(){
        document.getElementById('city').focus()
      },
      city(){
        document.getElementById('state').focus()
      },
      state(){
        document.getElementById('contactperson').focus()
      },
      contactperson(){
        document.getElementById('contactpersonphone').focus()
      },
      load() {
        this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/pemasoks/load')
        .then((ress)=>{
          this.dtsuplier = ress.data
        })
        .catch(() => {
          console.log(err)
        })
      },
      isNumber: function (evt) {
      evt = evt ? evt : window.event
      var charCode = evt.which ? evt.which : evt.keyCode
      if (
        charCode > 31 &&
        (charCode < 48 || charCode > 57) &&
        charCode !== 46
      ) {
        evt.preventDefault()
      } else {
        return true
      }
    },
     },
     mounted() {
      this.load()
     }
    }
</script>
 
