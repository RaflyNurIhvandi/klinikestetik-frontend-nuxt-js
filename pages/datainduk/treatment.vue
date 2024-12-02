<template>
  <div>
    <h2>Data Treatment</h2>

    <v-form @submit.prevent="save" v-model="isvalid" ref="form">
      <v-container>

        <!-- form -->
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field ref="tap1" v-model="form.namaproduk" type="text" :rules="nameRules" label="Name" required @keydown.enter="fild1"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field ref="tap2" v-model="form.hargajual" type="text" :rules="sellRules" label="Selling Price" required @keydown.enter="fild2" @keypress="isNumber($event)"></v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12">
            <v-text-field ref="tap3" v-model="form.deskripsi" :rules="descRules" label="Description" required @keydown.enter="fild3"></v-text-field>
          </v-col>
        </v-row><br>

        <!-- button -->
        <v-row>
          
        <v-col class="d-flex flex-row mb-6 bg-surface-variant mt-5" cols="12" sm="6" md="6" >  
        <v-btn color="success" type="reset" name="button" class="mr-4" @click="reset" v-show="!updateSubmit">
          New Treatment
        </v-btn>
        <v-btn ref="tap4" color="primary" class="mr-4" width="150px" name="button" @click="save" v-show="!updateSubmit">
          save
        </v-btn>
        <v-btn color="primary" class="mr-4" width="150px" name="button" v-show="updateSubmit" v-on:click="update(form)">
          save
        </v-btn>
        <v-btn color="error" class="mr-4" width="150px" @click="reset" type="reset">
          Cancel
        </v-btn>
        </v-col>

        <v-col class="text-end d-flex flex-row-reverse mb-6 bg-surface-variant mt-8" cols="12" sm="6" md="6" >
          <a @click="getCetak()">| Print</a>
          <a @click="getPdf()">| Export to PDF</a>
          <a @click="getExcel()">| Export to Excel</a>
        </v-col>

        </v-row>
      </v-container>
    </v-form>
    <!-- data table -->
    <div>
        <v-text-field v-model="cari" append-icon="mdi-magnify" label="Search Product Name" single-line></v-text-field>
        <v-data-table :headers="headerdatatreat" :items="treatments" :search="cari" :items-per-page="5" class="elevation-1">
          <template v-slot:[`item.tools`]="{ item }">
            <v-btn small @click="edittreatment(item)">
              <v-icon small color="success">
                mdi-pencil
              </v-icon>
            </v-btn>
            <v-btn small @click="hapus(item)">
              <v-icon small color="error">
                 mdi-eraser
              </v-icon>
            </v-btn>
          </template>
        </v-data-table>
      </div>
  </div>
</template>

<script>
import axios from "axios";
import Swal from 'sweetalert2';

export default {
  data() {
    return {
      headerdatatreat:[
        { text: "No", value: "id" },
        { text: "Name", value: "namaproduk" },
        { text: "Sell Price", value: "hargajual" },
        { text: "Tools", value: "tools" }
      ],
       form:{
        id: "",
        kodegrup:"T",
        namaproduk:"",
        hargajual:"",
        deskripsi:""
      },
      edittreat:{
        id: "",
        kodegrup:"T",
        namaproduk:"",
        hargajual:"",
        deskripsi:""
      },
      valid: true,
      treatments: [],
      updateSubmit: false,
      nameRules: [
        (v) => !!v || 'Name is required',
        (v) => (v && v.length <= 10) || 'Name must be less than 10 characters',
      ],
      descRules: [
        (v) => !!v || 'Desc is required',
        (v) => (v && v.length <= 150) || 'you have been reach maximum character',
      ],
      sellRules: [
        (v) => !!v || 'Price is required',
        (v) => (v && v.length <= 150) || 'you have been reach maximum character',
      ],
      isvalid: false,
      cari: '',
    }
  },
  methods: {
    load() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl+'/treatment/T')
        .then((response) => {
          this.treatments = response.data.data
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
      })
        .then((result) => {
          if (result.isConfirmed) {
          this.apiurl = process.env.API_URL
          axios
        .post(this.apiurl+'/treatment/send/T', this.form)
        .then(() => {
          this.load();
          this.form.kodegrup = "";
          this.form.namaproduk = "";
          this.form.hargajual = "";
          this.form.deskripsi = "";
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
    edittreatment(item) {
      this.updateSubmit = true;
      this.edittreat = Object.assign({}, item);
      this.form.id = this.edittreat.id;
      this.form.namaproduk = this.edittreat.namaproduk;
      this.form.hargajual = this.edittreat.hargajual;
      this.form.deskripsi = this.edittreat.deskripsi;
    },
    update(form) {
      Swal.fire ({
        title: 'Do you want to save the changes?',
        showDenyButton: true,
        showCancelButton: true,
        confirmButtonText: 'Save',
        denyButtonText: `Don't save`,
      })
      .then((result) => {
        if (result.isConfirmed) {
        this.apiurl = process.env.API_URL
      axios
        .put(this.apiurl + '/treatment/update/T/' + form.id, {
          'kodegrup': this.form.kodegrup,
          'namaproduk' : this.form.namaproduk,
          'hargajual' : this.form.hargajual,
          'deskripsi' : this.form.deskripsi,
        })
        .then(() => {
          this.load();
          this.form.kodegrup = "";
          this.form.namaproduk = "";
          this.form.hargajual = "";
          this.form.deskripsi = "";
          this.updateSubmit = false;
        })
              Swal.fire('Saved!', '', 'success')
            } else if (result.isDenied) {
              Swal.fire('Changes are not saved', '', 'info')
            }
        })

        .catch(() => {
          alert("gagal")
        })
    },
    hapus(item) {
      Swal.fire({
        title: 'Are you sure?',
        text: "You won't be able to revert this!",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!'
      })
      .then((result) => {
      if (result.isConfirmed) {
        this.apiurl = process.env.API_URL
      axios.delete(this.apiurl+`/treatment/delete/T/${item.kodeproduk}`)
        .then(() => {
          this.load();
          this.form.kodegrup = "";
          this.form.namaproduk = "";
          this.form.hargajual = "";
          this.form.deskripsi = "";
          })
              Swal.fire(
                'Deleted!',
                'Your file has been deleted.',
                'success'
              )
            }
          })

        .catch(() => {
          alert("gagal")
        })
    },
    fild1(){
      this.$refs.tap2.focus()
    },
    fild2(){
      this.$refs.tap3.focus()
    },
    // fild3(){
    //   this.$refs.tap4.type = 'submit'
    // },
    reset () {
      this.$refs.form.reset()
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
   getCetak() {
    this.apiurl = process.env.API_URL
      let uri = this.apiurl+'/cetak/T';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
    getExcel() {
    this.apiurl = process.env.API_URL
      let uri = this.apiurl+'/excel/T';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
    getPdf() {
    this.apiurl = process.env.API_URL
      let uri = this.apiurl+'/exportPDF/T';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
  },
  mounted() {
    this.$refs.tap1.focus();
    this.load();
  },
  }
</script>
