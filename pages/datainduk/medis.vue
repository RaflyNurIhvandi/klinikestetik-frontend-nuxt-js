<template>
  <div>
    <!-- menyimpan ke tabel product dengan kode "M" -->
    <h1>{{ judulhalaman }}</h1>
    <v-form>
      <v-container>
        <v-row>
          <!-- namaproduk -->
          <v-col cols="12" sm="6" md="6">
            <v-text-field ref="namaproduk" v-model="damed.namaproduk" label="Name" :rules="CommonFieldRules"
              @keydown.enter="nexttab1"></v-text-field>
          </v-col>
          <!-- hargajual -->
          <v-col cols="12" sm="6" md="6">
            <v-text-field ref="hargajual" v-model="damed.hargajual" type="number" label="Selling Price"
              @keydown.enter="getharju($event)" :rules="KomisiFieldRules"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <!-- deskripsi -->
          <v-col cols="12" sm="12" md="12">
            <v-text-field ref="deskripsi" v-model="damed.deskripsi" label="deskripsi"
              :rules="CommonFieldRules"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-btn v-show="!showeditbtn" type="button" color="green" class="mx-2" elevation="2" @click="submit">New
              Medis</v-btn>
            <v-btn v-show="showeditbtn" color="blue" elevation="2" class="mx-2" @click="updatedamed">Save</v-btn>
            <v-btn color="red" elevation="2" @click="cancelupdatedamed">Cancel</v-btn>
          </v-col>
          <v-col>
            <v-btn @click="cetak">| Print |</v-btn>
            <v-btn @click="cetakpdf"> Export to PDF</v-btn>
            <v-btn @click="cetakexel">| Export to Excel |</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <v-text-field v-model="cari" append-icon="mdi-magnify" label="Pencarian" single-line></v-text-field>
    <v-data-table :headers="headerdatamedis" :items="datamedis" :items-per-page="5" class="elevation-1">
      <template v-slot:item.aksi="{ item }">
        <v-btn color="blue" @click="editdamed(item)">
          <!-- <v-icon small class="mr-2" color="success" style="border: solid thin green;"> -->
          Edit
          <!-- </v-icon> -->
        </v-btn>
        <v-btn color="red" @click="delmed(item)">
          <!-- <v-icon small class="mr-2" color="error" style="border: solid thin red;"> -->
          Hapus
          <!-- </v-icon> -->
        </v-btn>
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
      judulhalaman: "Data Medis",
      headerdatamedis: [
        { text: 'Name', value: 'namaproduk' },
        { text: 'Selling Price', value: 'hargajual' },
        { text: 'Aksi', value: 'aksi' },
      ],
      cari: "",
      datamedis: [],
      // icons: {
      //   mdiAccount,
      //   mdiPencil,
      //   mdiShareVariant,
      //   mdiDelete,
      // },
      damed: {
        namaproduk: "",
        hargajual: "",
        kodegrup: "M",
        deskripsi: "",
      },
      editdatamedis: {
        namaproduk: "",
        hargajual: "",
        deskripsi: "",
      },
      dmd: [],
      editedIndex: -1,
      apiurl: '',
      CommonFieldRules: [
        value => {
          if (value?.length > 0) return true
          return 'Cannot Empty . . '
        }
      ],
      KomisiFieldRules: [
        value => {
          if (/[^a-z]/.test(value)) return true
          return 'Only Number'
        }
      ],
      showeditbtn: false
    }
  },
  created() {

  },
  methods: {
    nexttab1() {
      this.$refs.hargajual.focus();
    },
    cetak() {
      window.open(this.apiurl + '/damed/cetak', "cetak", "width=800,height=800,left=300,top=200");
    },
    cetakpdf() {
      // alert('cetak')
      let uri = this.apiurl + '/damed/cetakpdf'
      window.open(uri, 'cetakpdf', "width=800,height=800,left=300,top=200");
    },
    cetakexel() {
      let uri = this.apiurl + '/damed/cetakexel';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
    getharju(event) {
      let a = event.target.value
      let b = Number(a).toLocaleString()
      this.damed.hargajual = b
      this.$nextTick(() => {
        this.$refs.deskripsi.focus();
      })
    },
    submit(e) {
      console.log(this.damed.namaproduk)
      if (this.damed.namaproduk == "" || this.damed.hargajual == "" || this.damed.deskripsi == "") {
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
              this.$axios.post(this.apiurl + '/damed/store', this.damed)
                .then(() => {
                  this.successalert();
                  this.loaddata();
                  this.cancelupdatedamed();
                  this.showeditbtn = false
                }).catch(() => {
                  this.erroralert();
                })
            }
          })
          .catch(() => {
            this.erroralert();
          })
      }
    },
    loaddata() {
      this.apiurl = process.env.API_URL
      this.$axios.get(this.apiurl + '/damed/index')
        .then((res) => {
          this.datamedis = res.data.data
          // this.cleanupdatedamed();
        }).catch(() => {
          this.erroralert();
        })
    },
    delmed(item) {
      Swal.fire({
        title: 'Are you sure ?',
        text: "You won't be able to revert this!",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!'
      })
        .then((result) => {
          if (result.isConfirmed) {
            this.$axios.delete(this.apiurl + '/damed/delete/' + item.id)
              .then((res) => {
                // console.log(res.data.data)
                this.loaddata();
                this.successalert();
              }).catch(() => {
                this.erroralert();
              })
          }
        })
        .catch(() => {
          this.erroralert();
        })
    },
    async editdamed(item) {
      this.editdatamedis = Object.assign({}, item)

      this.damed.namaproduk = this.editdatamedis.namaproduk,
        this.damed.hargajual = this.editdatamedis.hargajual,
        this.damed.deskripsi = this.editdatamedis.deskripsi,
        this.showeditbtn = true
    },
    updatedamed() {
      Swal.fire({
        title: 'Are you sure ?',
        text: "You won't be able to revert this!",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes!'
      })
        .then((result) => {
          if (result.isConfirmed) {
            this.$axios.put(this.apiurl + '/damed/update/' + this.editdatamedis.id, {
              namaproduk: this.damed.namaproduk,
              hargajual: this.damed.hargajual,
              deskripsi: this.damed.deskripsi
            }).then(() => {
              this.cancelupdatedamed();
              this.loaddata();
              this.successalert();
              this.showeditbtn = false
            }).catch(() => {
              this.loaddata();
              this.erroralert();
            })
          }
        })
    },
    cancelupdatedamed() {
      this.damed.namaproduk = ""
      this.damed.hargajual = ""
      this.damed.deskripsi = ""
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


  },
  mounted() {
    this.loaddata();
    this.$refs.namaproduk.focus()

  },
}
</script>
