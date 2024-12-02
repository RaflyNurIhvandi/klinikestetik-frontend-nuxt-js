<template>
  <div>
    <h1>{{ judulhalaman }}</h1>
    <v-form>
      <v-container>
        <v-row>
          <v-col cols="12" sm="6" md="4">
            <v-text-field label="Name" ref="tab1" v-model="form.namapegawai" :rules="CommonFieldRules"
              @keydown.enter="nextfield1"></v-text-field>
          </v-col>
          <v-col cols="12" sm="6" md="8">
            <v-text-field label="Address" ref="tab2" v-model="form.alamatpegawai" :rules="CommonFieldRules"
              @keydown.enter="nextfield2"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="12" md="4">
            <v-text-field label="Phone Number" ref="tab3" type="number" v-model="form.notelppegawai"
              :rules="CommonFieldRules" @keydown.enter="nextfield3"></v-text-field>
          </v-col>
          <v-col cols="12" sm="12" md="4">
            <v-text-field label="City" ref="tab4" v-model="form.kotapegawai" :rules="CommonFieldRules"
              @keydown.enter="nextfield4"></v-text-field>
          </v-col>
          <v-col cols="12" sm="12" md="4">
            <v-text-field label="State" ref="tab5" v-model="form.statepegawai" :rules="CommonFieldRules"
              @keydown.enter="nextfield5"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="12" md="4">
            <!-- <v-text-field label="Profession" v-model="form.profesi" :rules="CommonFieldRules"></v-text-field> -->
            <v-select ref="tab6" @keydown.enter="nextfield6" :items="items" v-model="form.jabatanpegawai"
              :rules="CommonFieldRules" label="Category" @change="cekvalue"></v-select>
            <!--
                      generate kode pegawai :
                      dokter d
                      terapis t
                      asisten a
                      pegawai p
                      masuk ke tabel pegawais

                      -->
          </v-col>
          <v-col cols="12" sm="12" md="4">
            <v-text-field ref="tab7" label="Incentive / Commission" type="text" v-model="form.komisi"
              @keydown.enter="digitkomisi($event)" :rules="KomisiFieldRules"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-btn v-show="showedit" color="green" elevation="2" class="mx-2" @click="submit">New partners</v-btn>
            <v-btn v-show="!showedit" color="blue" elevation="2" class="mx-2" @click="updatedokterpisten">Save</v-btn>
            <v-btn color="red" elevation="2" @click="cancel">Cancel</v-btn>
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
    <v-data-table :headers="headerdokterterapis" :items="dokterterapis" :items-per-page="5" class="elevation-1">
      <template v-slot:item.tools="{ item }">
        <v-btn color="blue" @click="editdokterapisten(item)">
          <!-- <v-icon small class="mr-2" color="success" style="border: solid thin green;"> -->
          Edit
          <!-- </v-icon> -->
        </v-btn>
        <v-btn color="red" @click="deletedoktersisten(item)">
          <!-- <v-icon small class="mr-2" color="error" style="border: solid thin red;"> -->
          Hapus
          <!-- </v-icon> -->
        </v-btn>
      </template>
    </v-data-table>
  </div>
</template>
<!--
  backend
  masuk ke db pegawais
  bedanya di jabatan
 -->
<script>
import { nextTick } from 'process'
import Swal from 'sweetalert2'
// import {
//   mdiAccount,
//   mdiPencil,
//   mdiShareVariant,
//   mdiDelete,
// } from '@mdi/js'
export default {
  data() {
    return {
      items: [{ text: 'Dokter', value: 'D' }, { text: 'Terapis', value: 'T' }, { text: 'Asisten', value: 'A' }],
      // items: [{ text: 'Terapis' }, { text: 'Dokter' }, { text: 'Asisten' }],
      judulhalaman: "Dokter, Terapis, dan Asisten",
      headerdokterterapis: [
        // { text: 'No.', value: 'no' },
        { text: 'Customer Id', value: 'kodepegawai' },
        { text: 'Name', value: 'namapegawai' },
        { text: 'Address', value: 'alamatpegawai' },
        { text: 'City', value: 'kotapegawai' },
        { text: 'Tools', value: 'tools' },
      ],
      cari: "",
      dokterterapis: [],
      form: {
        namapegawai: "",
        alamatpegawai: "",
        notelppegawai: "",
        kotapegawai: "",
        statepegawai: "",
        jabatanpegawai: "",
        komisi: "",
      },
      editform: {
        namapegawai: "",
        alamatpegawai: "",
        notelppegawai: "",
        kotapegawai: "",
        provinsipegawai: "",
        jabatanpegawai: "",
        komisi: "",
      },
      // icons: {
      //   mdiAccount,
      //   mdiPencil,
      //   mdiShareVariant,
      //   mdiDelete,
      // },
      trps: [],
      apiurl: '',
      showedit: true,
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
      ]
    }
  },
  methods: {
    nextfield1() {
      this.$refs.tab2.focus()
    },
    nextfield2() {
      this.$refs.tab3.focus()
    },
    nextfield3() {
      this.$refs.tab4.focus()
    },
    nextfield4() {
      this.$refs.tab5.focus()
    },
    nextfield5() {
      this.$refs.tab6.focus()
    },
    nextfield6() {
      this.$refs.tab7.focus()
    },
    cetak() {
      window.open(this.apiurl + '/dokterasisten/cetak', "cetak", "width=800,height=800,left=300,top=200");
    },
    cetakpdf() {
      let uri = this.apiurl + '/dokterasisten/cetakpdf'
      window.open(uri, 'cetakpdf', "width=800,height=800,left=300,top=200");
    },
    cetakexel() {
      let uri = this.apiurl + '/dokterasisten/cetakexel';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
    digitkomisi(event) {
      console.log(event.target.value)
      let trgt = event.target.value
      let hsl = Number(trgt).toLocaleString()
      this.form.komisi = hsl
    },
    cancel() {
      this.showedit = true
      this.form.namapegawai = ""
      this.form.alamatpegawai = ""
      this.form.notelppegawai = ""
      this.form.kotapegawai = ""
      this.form.statepegawai = ""
      this.form.jabatanpegawai = ""
      this.form.komisi = ""
    },
    cleanform() {
      this.form.namapegawai = ""
      this.form.alamatpegawai = ""
      this.form.notelppegawai = ""
      this.form.kotapegawai = ""
      this.form.statepegawai = ""
      this.form.jabatanpegawai = ""
      this.form.komisi = ""
    },
    deletedoktersisten(item) {
      // alert(item.id)
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
            this.$axios.delete(this.apiurl + '/dokterasisten/delete/' + item.id)
              .then(() => {
                this.loaddata();
                this.successalert();
              })
              .catch(() => {
                this.erroralert();
              })
          }
        })
    },
    updatedokterpisten() {
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
            this.$axios.put(this.apiurl + '/dokterasisten/update/' + this.editform.id, {
              namapegawai: this.form.namapegawai,
              alamatpegawai: this.form.alamatpegawai,
              notelppegawai: this.form.notelppegawai,
              kotapegawai: this.form.kotapegawai,
              statepegawai: this.form.statepegawai,
              jabatanpegawai: this.form.jabatanpegawai,
              komisi: this.form.komisi,
            }).then(() => {
              this.$route
              this.showedit = true
              this.cleanform();
              this.loaddata();
              this.successalert();
            }).catch(() => {
              this.loaddata();
              this.erroralert();
            })
          }
        })
    },
    editdokterapisten(item) {
      console.log(item)
      this.showedit = false
      this.editform = Object.assign({}, item)

      this.form.namapegawai = this.editform.namapegawai,
        this.form.alamatpegawai = this.editform.alamatpegawai,
        this.form.notelppegawai = this.editform.notelppegawai,
        this.form.kotapegawai = this.editform.kotapegawai
      this.form.statepegawai = this.editform.provinsipegawai,
        this.form.jabatanpegawai = this.editform.jabatanpegawai,
        this.form.komisi = this.editform.komisi
    },
    submit() {
      if (this.form.namapegawai != "" && this.form.alamatpegawainamapegawai != "" && this.notelppegawai != "") {
        this.$axios.post(this.apiurl + '/dokterasisten/store ', this.form)
          .then(() => {
            this.loaddata();
            this.successalert();
            this.form.namapegawai = "",
              this.form.alamatpegawai = "",
              this.form.notelppegawai = "",
              this.form.kotapegawai = "",
              this.form.statepegawai = "",
              this.form.jabatanpegawai = "",
              this.form.komisi = ""
          }).catch(() => {
            this.erroralert();
          })
      } else {
        this.erroralert();
      }
    },
    loaddata() {
      this.apiurl = process.env.API_URL

      this.$axios.get(this.apiurl + '/dokterasisten/index')
        .then((res) => {
          this.dokterterapis = res.data.data
        }).catch(() => {
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
    cekvalue() {
      nextTick(() => {
        // alert(this.$refs.profesi.value)
      })
    }
  },

  mounted() {
    this.loaddata();
    this.$refs.tab1.focus();
  },
}
</script>
