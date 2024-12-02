<template>
  <div>
    <h1>{{ judulhalaman }}</h1>
    <v-form>
      <v-container>
        <v-row>
          <v-col cols="12" sm="6" md="4">
            <v-text-field ref="namapelanggan" label="Name" v-model="cust.namapelanggan" :rules="CommonFieldRules"
              @keydown.enter="nextfield1"></v-text-field>
          </v-col>
          <v-col cols="12" sm="6" md="8">
            <v-text-field ref="alamatpelanggan" label="Address" v-model="cust.alamatpelanggan" :rules="CommonFieldRules"
              @keydown.enter="nextfield2"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="12" md="4">
            <v-text-field ref="notelppelanggan" type="number" label="Phone Number" v-model="cust.notelppelanggan"
              :rules="CommonFieldRules" @keydown.enter="nextfield3"></v-text-field>
          </v-col>
          <v-col cols="12" sm="12" md="4">
            <v-text-field ref="kotapelanggan" label="City" v-model="cust.kotapelanggan" :rules="CommonFieldRules"
              @keydown.enter="nextfield4"></v-text-field>
          </v-col>
          <v-col cols="12" sm="12" md="4">
            <v-text-field ref="statepelanggan" label="State" v-model="cust.statepelanggan" :rules="CommonFieldRules"
              @keydown.enter="nextfield5"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
            <v-select ref="kategoripelanggan" :items="items" v-model="cust.kategoripelanggan" label="Category"></v-select>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-btn ref="simpanplgn" v-show="!showeditbtn" color="green" elevation="2" @click="simpanpelanggan">New
              customers</v-btn>
            <v-btn ref="simpanplgn2" v-show="showeditbtn" color="blue" elevation="2" class="mx-2"
              @click="updatecustomer">Save</v-btn>
            <v-btn color="red" elevation="2" @click="canceledit">Cancel</v-btn>
          </v-col>
          <v-col class="text-end">
            <v-btn @click="cetak">| Print</v-btn>
            <v-btn @click="exportpdf">| Export to PDF</v-btn>
            <v-btn @click="exportexel">| Export to Excel |</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <v-text-field v-model="cari" append-icon="mdi-magnify" label="Pencarian" single-line></v-text-field>
    <v-data-table :headers="headerdatacust" :items="datacust" :items-per-page="5" class="elevation-1">
      <template v-slot:item.aksi="{ item }">
        <v-btn color="blue" @click="editcustomer(item)">
          <!-- <v-icon small class="mr-2" color="success" style="border: solid thin green;"> -->
          Edit
          <!-- </v-icon> -->
        </v-btn>
        <v-btn color="red" @click="deletecust(item)">
          <!-- <v-icon small class="mr-2" color="error" style="border: solid thin red;"> -->
          Hapus
          <!-- </v-icon> -->
        </v-btn>
        <!-- <v-btn :to="`/datacustomer/${item.id}`">
          detail
        </v-btn> -->

      </template>
    </v-data-table>
  </div>
</template>
<script>
import Swal from 'sweetalert2'

// import {
//   mdiAccount,
//   mdiPencil,
//   mdiShareVariant,
//   mdiDelete,
// } from '@mdi/js'
export default {
  name: "DefaultLayout",
  data() {
    return {
      items: ['Member', 'VIP', 'VVIP'],
      judulhalaman: "Data Customers",
      headerdatacust: [
        { text: 'Customer Id', value: 'kodepelanggan' },
        { text: 'Name', value: 'namapelanggan' },
        { text: 'Address', value: 'alamatpelanggan' },
        { text: 'City', value: 'kotapelanggan' },
        { text: 'Tools', value: 'aksi' },
      ],
      cari: "",
      datacust: [],
      // icons: {
      //   mdiAccount,
      //   mdiPencil,
      //   mdiShareVariant,
      //   mdiDelete,
      // },
      cust: {
        namapelanggan: "",
        alamatpelanggan: "",
        notelppelanggan: "",
        kotapelanggan: "",
        statepelanggan: "",
        kategoripelanggan: "",
      },
      editcust: {
        namapelanggan: "",
        alamatpelanggan: "",
        notelppelanggan: "",
        kotapelanggan: "",
        statepelanggan: "",
        kategoripelanggan: "",
      },
      apiurl: '',
      CommonFieldRules: [
        value => {
          if (value?.length > 0) return true
          return 'Cannot Empty . . '
        }
      ],
      showeditbtn: false,
    }
  },
  created() {

  },
  methods: {
    nextfield1() {
      this.$refs.alamatpelanggan.focus()
    },
    nextfield2() {
      this.$refs.notelppelanggan.focus()
    },
    nextfield3() {
      this.$refs.kotapelanggan.focus()
    },
    nextfield4() {
      this.$refs.statepelanggan.focus()
    },
    nextfield5() {
      this.$refs.kategoripelanggan.focus()
    },
    // nextfield6(){
    //   this.$refs.simpanplgn.focus()
    // },
    // nextfield6(){
    //   if(this.showeditbtn = false){
    //     this.$refs.simpanplgn.focus();
    //   }else if(this.showeditbtn = true){
    //     this.$refs.simpanplgn2.focus()
    //   }
    // },
    cetak() {
      window.open(this.apiurl + '/pelanggan/cetak', "cetak", "width=800,height=800,left=300,top=200");
    },
    exportpdf() {
      // alert('cetak')
      let uri = this.apiurl + '/pelanggan/cetakpdf'
      window.open(uri, 'cetakpdf', "width=800,height=800,left=300,top=200");
    },
    exportexel() {
      let uri = this.apiurl + '/pelanggan/cetakexel';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
    simpanpelanggan() {
      if (this.cust.namapelanggan == "" || this.cust.kategoripelanggan == "" || this.cust.notelppelanggan == "" || this.cust.alamatpelanggan == "") {
        this.erroralert();
      } else {
        Swal.fire({
          title: 'Are you sure?',
          text: "You won't be able to revert this!",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Yes!'
        })
          .then((result) => {
            if (result.isConfirmed) {
              this.$axios.post(this.apiurl + '/pelanggan/store', this.cust)
                .then(() => {
                  this.load();
                  this.successalert();
                })
                .catch(() => {
                  this.erroralert();
                })
            }
          })
      }
    },
    load() {
      this.apiurl = process.env.API_URL
      this.$axios.get(this.apiurl + '/pelanggan/index').then((res) => {
        this.datacust = res.data.data
      })
      this.$refs.namapelanggan.focus()
    },
    editcustomer(item) {
      this.editcust = Object.assign({}, item);
      this.cust.alamatpelanggan = this.editcust.alamatpelanggan,
        this.cust.kategoripelanggan = this.editcust.kategoripelanggan,
        this.cust.kotapelanggan = this.editcust.kotapelanggan,
        this.cust.namapelanggan = this.editcust.namapelanggan,
        this.cust.statepelanggan = this.editcust.statepelanggan,
        this.cust.notelppelanggan = this.editcust.notelppelanggan,

        this.showeditbtn = true
    },
    updatecustomer() {
      Swal.fire({
        title: 'Are you sure?',
        text: "You won't be able to revert this!",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes!'
      })
        .then((result) => {
          if (result.isConfirmed) {
            this.$axios.put(this.apiurl + '/pelanggan/update/' + this.editcust.id, {
              namapelanggan: this.cust.namapelanggan,
              alamatpelanggan: this.cust.alamatpelanggan,
              kotapelanggan: this.cust.kotapelanggan,
              kategoripelanggan: this.cust.kategoripelanggan,
              statepelanggan: this.cust.statepelanggan,
              notelppelanggan: this.cust.notelppelanggan
            }).then(() => {
              this.successalert();
              this.showeditbtn = true;
              this.load();
              this.canceledit();
              console.log(this.CommonFieldRules.value)
            }).catch(() => {
              this.erroralert();
            })
          }
        })
    },
    deletecust(item) {
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
            this.$axios.delete(this.apiurl + '/pelanggan/delete/' + item.id)
              .then((res) => {
                console.log(res)
                Swal.fire(
                  'Deleted!',
                  'Your file has been deleted.',
                  'success'
                )
                this.load();
              })
          }
        })
        .catch(() => {
          this.erroralert();
        })
    },
    successalert() {
      Swal.fire({
        position: 'top-end',
        icon: 'success',
        title: 'Data Tersimpan',
        showConfirmButton: false,
        timer: 1500
      })
    },
    erroralert() {
      Swal.fire({
        icon: "error",
        title: "Oops...",
        text: "Something went wrong!",
        footer: ""
      });
    },
    canceledit() {
      this.showeditbtn = false
      this.cust.namapelanggan = "",
        this.cust.alamatpelanggan = "",
        this.cust.notelppelanggan = "",
        this.cust.kotapelanggan = "",
        this.cust.statepelanggan = "",
        this.cust.kategoripelanggan = ""
    }
  },
  mounted() {
    this.load();
  }
}
</script>
