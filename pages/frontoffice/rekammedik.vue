<template>
  <div>
    <h1>Medical Record</h1>
    <section>
      <v-row>
        <v-col cols="auto" class="me-auto">
          <v-sheet>
            <v-text-field label="customer" append-icon="mdi-magnify" ref="kodepelanggan"
              @click:append="modalcaripelanggan" v-model="medic.kodepelanggan"
              @keydown.enter="getcustomer"></v-text-field>
          </v-sheet>
        </v-col>
        <v-col cols="auto">
          <v-sheet>
            <v-text-field type="date" label="Date" v-model="medic.tanggal"></v-text-field>
          </v-sheet>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="auto" class="me-auto">
          <v-sheet>
            <p><span style="font-weight:bolder; font-size: large;">{{ this.rekmed.namapelanggan }} </span></p>
            <p>{{ this.rekmed.alamatpelanggan }} {{ this.rekmed.kotapelanggan }} {{ this.rekmed.notelppelanggan }}</p>
          </v-sheet>
        </v-col>
        <v-col cols="auto">
          <v-sheet>
            <v-text-field v-model="medic.noinvoice" @click="dialog = true" label="No. Invoice"></v-text-field>
          </v-sheet>
        </v-col>
      </v-row>
    </section>
    <div class="text-center">
      <v-dialog v-model="findcust" width="auto">
        <v-card>
          <v-card-title>
            Finding Customer
            <v-spacer></v-spacer>
            <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line
              hide-details></v-text-field>
          </v-card-title>
          <v-data-table :headers="headers" :items="cust" :search="search">
            <template v-slot:item.aksi="{ item }">
              <v-btn color="blue" @click="pilihcust(item)">
                Pilih
              </v-btn>
            </template>
          </v-data-table>
        </v-card>
      </v-dialog>
    </div>
    <div class="text-center">
      <v-dialog v-model="dialog" width="auto">
        <v-card>
          <v-simple-table>
            <template v-slot:default>
              <thead>
                <tr>
                  <th class="text-left">
                    No. Faktur
                  </th>
                  <th class="text-left">
                    Tanggal Jual
                  </th>
                  <th class="text-left">
                    Status
                  </th>
                  <th class="text-left">
                    #Act
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in invoice" :key="item.id">
                  <td>{{ item.nofaktur }}</td>
                  <td>{{ item.tgljual }}</td>
                  <td>{{ item.statusbayar }}</td>
                  <td>
                    <v-btn @click="pilihinvoice(item.nofaktur)">Pilih</v-btn>
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-card>
      </v-dialog>
    </div>
    <div class="text-center">
      <v-dialog v-model="dialog2" :scrim="false" persistent width="auto">
        <v-card color="primary">
          <v-card-text>
            Finding Data, Please Wait . . . .
            <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
    <div class="text-center">
      <v-dialog v-model="dialog3" :scrim="false" persistent width="auto">
        <v-card color="primary">
          <v-card-text>
            Uploading, Please Wait . . . .
            <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
    <section>
      <v-textarea v-model="medic.diagnosa" label="Diagnosa" outlined></v-textarea>
      <v-textarea v-model="medic.produk" label="Produk yang digunakan" outlined></v-textarea>
      <v-textarea v-model="medic.keterangan" label="Keterangan" outlined></v-textarea>
    </section>
    <section>
      <v-row>
        <v-col cols="lg-4" class="me-auto">
          <v-sheet>
            <v-file-input ref="foto1" id="foto1" name="foto1" accept="image/*" type="file" label="Foto 1"></v-file-input>
          </v-sheet>
        </v-col>
        <v-col cols="lg-4">
          <v-sheet>
            <v-text-field label="Keterangan gambar" v-model="medic.keterangan1"></v-text-field>
          </v-sheet>
        </v-col>
        <v-col cols="lg-4">
          <v-sheet>
            <v-btn>Add New</v-btn>
            <v-btn @click="saveinvoice">Save</v-btn>
          </v-sheet>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="lg-4" class="me-auto">
          <v-sheet>
            <v-file-input ref="foto2" accept="image/*" label="Foto 2"></v-file-input>
          </v-sheet>
        </v-col>
        <v-col cols="lg-4">
          <v-sheet>
            <v-text-field label="Keterangan gambar" v-model="medic.keterangan2"></v-text-field>
          </v-sheet>
        </v-col>
        <v-col cols="lg-4">
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="lg-4" class="me-auto">
          <v-sheet>
            <v-file-input ref="foto3" accept="image/*" label="Foto 3"></v-file-input>
          </v-sheet>
        </v-col>
        <v-col cols="lg-4">
          <v-sheet>
            <v-text-field label="Keterangan gambar" v-model="medic.keterangan3"></v-text-field>
          </v-sheet>
        </v-col>
        <v-col cols="lg-4">
        </v-col>
      </v-row>
    </section>
  </div>
</template>
<script>
import axios from 'axios';
import Swal from 'sweetalert2'

export default {
  data() {
    return {
      search: '',
      headers: [
        {
          text: 'Nama Pelanggan',
          align: 'start',
          sortable: false,
          value: 'namapelanggan',
        },
        { text: 'Kode', value: 'kodepelanggan' },
        { text: 'Alamat', value: 'alamatpelanggan' },
        { text: 'No Telp', value: 'notelppelanggan' },
        { text: 'Pilih', value: 'aksi' },
      ],
      cust: [],
      findcust: false,
      dialog: false,
      dialog2: false,
      dialog3: false,
      files: [],
      medic: {
        kodepelanggan: "",
        tanggal: "",
        noinvoice: "", // not in table
        diagnosa: "",
        produk: "",
        keterangan: "",
        foto1: "",
        foto2: "",
        foto3: "",
        keterangan1: "",
        keterangan2: "",
        keterangan3: "",
      },
      foto1: "",
      foto2: "",
      foto3: "",
      apiurl: '',
      rekmed: {
        namapelanggan: "",
        alamatpelanggan: "",
        notelppelanggan: "",
        kotapelanggan: "",
      },
      rekmed2: [],
      invoice: [],
      hsl: '',
    }
  },
  watch: {
    dialog2(val) {
      if (!val) return
      setTimeout(() => (this.dialog2 = false), 2000)
    },
    dialog3(val) {
      if (!val) return
      setTimeout(() => (this.dialog2 = false), 2000)
    },
  },
  methods: {
    pilihcust(item) {
      console.log(item)
      this.medic.kodepelanggan = item.kodepelanggan
      this.rekmed.namapelanggan = item.namapelanggan
      this.rekmed.alamatpelanggan = item.alamatpelanggan
      this.rekmed.kotapelanggan = item.kotapelanggan
      this.rekmed.notelppelanggan = item.notelppelanggan
      this.findcust = false
    },
    findingcust() {
      this.$axios.get(this.apiurl + '/rekammedis/getallcust')
        .then((res) => {
          this.cust = res.data.data
        })
    },
    modalcaripelanggan() {
      this.findcust = true
    },
    async getcustomer(event) {
      this.apiurl = process.env.API_URL
      let idcust = this.$refs.kodepelanggan.value
      await this.dialogue();
      await this.$axios.get(this.apiurl + '/rekammedis/getcust/' + idcust)
        .then((res) => {
          if (res.data.data.length == 0) {
            this.erroralert2();
          } else {
            this.$nextTick(() => {
              this.dialog2 = false
              // this.rekmed2 = res.data
              this.rekmed.namapelanggan = res.data.data[0].namapelanggan,
                this.rekmed.alamatpelanggan = res.data.data[0].alamatpelanggan,
                this.rekmed.notelppelanggan = res.data.data[0].notelppelanggan,
                this.rekmed.kotapelanggan = res.data.data[0].kotapelanggan
            })
          }
        })
      this.getinvoice();
    },
    /* --------loading-------- */
    dialogue() {
      this.dialog2 = true
    },
    dialogue2() {
      this.dialog3 = true
    },
    /* -------------------- */
    getdate() {
      this.$axios.get(this.apiurl + '/rekammedis/getdate')
        .then((res) => {
          this.medic.tanggal = res.data
        })
    },
    getinvoice() {
      this.$axios.get(this.apiurl + '/rekammedis/getinvoice/' + this.medic.kodepelanggan)
        .then((res) => {
          this.invoice = res.data.data
        })
    },
    pilihinvoice(nofaktur) {
      this.medic.noinvoice = nofaktur
      this.dialog = false
    },
    async saveinvoice() {
      this.foto1 = this.$refs.foto1.internalArrayValue[0];
      this.foto2 = this.$refs.foto2.internalArrayValue[0];
      this.foto3 = this.$refs.foto3.internalArrayValue[0];

      let invo = new FormData();
      invo.append('nofaktur', this.medic.noinvoice)
      invo.append('tanggal', this.medic.tanggal)
      invo.append('kodepelanggan', this.medic.kodepelanggan)
      invo.append('diagnosa', this.medic.diagnosa)
      invo.append('produk', this.medic.produk)
      invo.append('keterangan', this.medic.keterangan)
      invo.append('foto1', this.foto1)
      invo.append('keterangan1', this.medic.keterangan1)
      invo.append('foto2', this.foto2)
      invo.append('keterangan2', this.medic.keterangan2)
      invo.append('foto3', this.foto3)
      invo.append('keterangan3', this.medic.keterangan3)

      await this.dialogue2();
      await this.$axios.post(this.apiurl + '/rekammedis/store', invo,
        {
          headers: {
            'ContentType': 'multipart/form-data'
          }
        })
        .then((res) => {
          // let asd = res.data.success
          // console.log(asd)
          if (res.data.success == true) {
            this.successalert();
          } else {
            this.erroralert();
          }
          this.dialog3 = false
        }).catch((res) => {
          if (res.data.success == false) {
            this.erroralert();
          }
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
      });
    },
    erroralert2() {
      Swal.fire({
        icon: "error",
        title: "Data Missing",
        text: "No data available!",
      });
    },
  },
  mounted() {
    this.getdate();
    this.findingcust();
    // this.getinvoice();
  },
}
</script>
