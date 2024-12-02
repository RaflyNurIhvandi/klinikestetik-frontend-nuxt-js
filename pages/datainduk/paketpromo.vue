<template>
  <div>
    <h1>Data Paket Promo</h1>
    <!-- form -->
    <v-form @submit.prevent="save" v-model="isvalid" ref="form">
      <v-container>
        <!-- form 1 -->
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field required ref="tap1" v-model="dtpack.namapaket" label="Product Name"
              @keydown.enter="field1"></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field required ref="tap2" v-model="dtpack.jmlitem" label="Stock" type="number"
              @keydown.enter="field2"></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field ref="tap3" v-model="dtpack.expired_date" type="date" label="Expired Date"
              @keydown.enter="field3"></v-text-field>
          </v-col>
        </v-row>

        <!-- form 2 -->
        <v-row>
          <v-col cols="12" md="8">
            <v-text-field ref="tap4" v-model="dtpack.deskripsi" label="Description"
              @keydown.enter="field4"></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field required ref="tap5" v-model="getgrandtotal" label="Selling price" @keydown.enter="field5"
              disabled></v-text-field>
          </v-col>
        </v-row>

        <!-- tombol -->
        <v-row>
          <v-col class="d-flex flex-row mb-6 bg-surface-variant mt-5" cols="12" ms="6" md="6">

            <v-btn style="color: white;" color="orange" width="200px" @click="dialog = true">
              Tambah item paket
            </v-btn>

          </v-col>
          <v-col class="text-end d-flex flex-row-reverse mb-6 bg-surface-variant mt-7" cols="12" ms="6" md="6">
            <v-btn color="error" class="mr-4" width="150px" @click="reset">
              Cancel
            </v-btn>

            <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" @click="simpan">Save</v-btn>


            <v-btn class="mr-4" color="success" width="150px" @click="newproduct">
              New Product
            </v-btn>
          </v-col>
        </v-row>

      </v-container>
    </v-form>

    <!-- table -->
    <v-data-table :headers="paketproduk" :items="pakets" :items-per-page="5" class="elevation-1">
      <template v-slot:[`item.aksi`]="{ item }">
        <v-icon color="error" @click="edit(item)">
          mdi-pencil-box-multiple-outline
        </v-icon>
      </template>
      <template v-slot:[`item.jmlitem`]="props">
        <v-edit-dialog :return-value.sync="props.item.jmlitem" large persistent @save="save" @cancel="cancel" @open="open"
          @close="close">
          <div>{{ props.item.jmlitem }}</div>
          <template v-slot:input>
            <div class="mt-4 text-h6">
              Update Qty
            </div>
            <v-text-field v-model="props.item.jmlitem" :rules="[max25chars]" @change="gettotal" label="Edit" single-line
              counter autofocus></v-text-field>
          </template>
        </v-edit-dialog>
      </template>
    </v-data-table>
    <!-- dialog -->
    <div class="text-center">
      <v-dialog v-model="dialog" width="1100">
        <v-card>
          <div style="padding: 2%;">
            <v-text-field v-model="cari" append-icon="mdi-magnify" label="Pencarian" single-line></v-text-field>
            <v-data-table :headers="dialoghead" :items="pack" :search="cari" :items-per-page="3" class="elevation-1">
              <template v-slot:[`item.aksi`]="{ item }">
                <v-icon color="success" @click="masuk(item)">
                  mdi-plus
                </v-icon>
              </template>
            </v-data-table>
          </div>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';
export default {
  data() {
    return {
      cari: "",
      paketproduk: [
        { text: "No", value: "id" },
        { text: "kode produk", value: "kodeproduk" },
        { text: "Product Name", value: "namaproduk" },
        { text: "Price", value: "hargajual" },
        { text: "Qty", value: "jmlitem" },
        { text: "Total", value: "total" },
        // { text: "", value: "aksi" },
      ],
      dialoghead: [
        { text: "No", value: "id" },
        { text: "Batch Number", value: "kodeproduk" },
        { text: "Product Name", value: "namaproduk" },
        { text: "Selling Price", value: "hargajual" },
        { text: "Stock", value: "stokmin" },
        { text: "Tools", value: "aksi" },
      ],
      pakets: [],
      pack: [],
      dialog: false,
      updateSubmit: false,
      isvalid: false,
      dtpack: {
        id: "",
        namapaket: "",
        jmlitem: "",
        expired_date: "",
        deskripsi: "",
        hargaitem: "",
      },
      dtupdate: {
        id: "",
        namapaket: "",
        jmlitem: "",
        expired_date: "",
        deskripsi: "",
        hargaitem: "",
      },
      dtedit: {
        namaproduk: "",
        stokmin: "",
        tglkadaluarsa: "",
        deskripsi: "",
        hargajual: "",
      },
      dtload: {
        nama: "",
        stok: "",
        tgl: "",
        des: "",
        harga: "",
      },
      snack: false,
      // snackColor: '',
      // snackText: '',
      max25chars: v => v.length <= 25 || 'Input too long!',
      pagination: {},

    }
  },
  computed: {
    getgrandtotal() {
      let gt1 = this.pakets.reduce((acc, item) => acc + item.total, 0);

      this.dtpack.hargaitem = gt1
      return this.dtpack.hargaitem
    }

  },
  methods: {
    gettotal(event) {
      let cariIndex = this.pakets.findIndex(pkt => pkt.jmlitem === event);
      let as = this.pakets[cariIndex].hargajual
      this.pakets[cariIndex].total = as * event
    },
    save() {
      this.snack = true
    },
    cancel() {
      this.snack = true
    },
    open() {
      this.snack = true
    },
    close() {},
    field1() {
      this.$refs.tap2.focus()
    },
    field2() {
      this.$refs.tap3.focus()
    },
    field3() {
      this.$refs.tap4.focus()
    },
    field4() {
      this.$refs.tap5.focus()
    },
    field5() {
      this.$refs.tap1.focus()
    },
    load() {
      this.apiurl = process.env.API_URL

    },
    loadpack() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/pack')
        .then((ress) => {
          this.pack = ress.data
        })
        .catch(() => {
        })
    },
    newproduct() {
      this.$refs.form.reset()
      this.$refs.tap1.focus()
      let sdj = this.pakets.length
      this.pakets.splice(0, sdj)
    },
    reset() {
      this.$refs.form.reset()
      this.$refs.tap1.focus()
      let sdj = this.pakets.length
      this.pakets.splice(0, sdj)
    },
    simpan() {
      let q = this.pakets.length
      let w = this.dtpack.deskripsi
      let e = this.dtpack.expired_date
      let r = this.dtpack.namapaket
      let t = this.dtpack.jmlitem
      let y = this.getgrandtotal
      if(q == 0 || w == null || e == null || r == null || t == null || y == null){
        Swal.fire({
            icon: 'error',
            title: 'Gagal',
            text: 'Data gagal disimpan / Data Belum lengkap',
          })
      }else{
        Swal.fire({
          title: 'Pastikan data yang diisi benar',
          text: "Anda yakin?",
          icon: 'info',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Yes'
        }).then((result) => {
          if (result.isConfirmed) {
            let objek = {
              data1: this.dtpack,
              data2: this.pakets
            }
            this.apiurl = process.env.API_URL
            axios
              .post(this.apiurl + '/pack/save', objek)
              .then((res) => {
                console.log(res)
                // this.$refs.form.reset()
                // this.$refs.tap1.focus()
                // let sdj = this.pakets.length
                // this.pakets.splice(0, sdj)
              }).catch((res) => {
                console.log(res)
              })
            Swal.fire(
              'Success',
              'Data berhasil disimpan',
              'success'
            )
          }
        })
      }
    },
    masuk(item) {
      let lk = this.itemhargajual
      let kl = 1
      let jo = this.pakets.length + 1
      var my_object = {
        id: jo,
        kodeproduk: item.kodeproduk,
        namaproduk: item.namaproduk,
        hargajual: item.hargajual,
        jmlitem: kl,
        total: kl * item.hargajual
      };
      this.pakets.push(my_object)
      this.$nextTick(() => {
        let jk = this.pakets.slice(-1);
        let jkl = jk[0].jmlitem
        let idx = this.pakets.findIndex(pkt => pkt.kodeproduk === item.kodeproduk);
        let qt = this.pakets[idx].jmlitem //jumlah item sebelumnya
        let jml = this.pakets[idx].hargajual //jumlah item sebelumnya

        let pj = this.pakets.length - 1
        if (idx == pj) {
          this.pakets[idx].jmlitem = qt
        } else if (pj > idx) {
          let ttl = parseInt(qt) + parseInt(jkl)
          let ttl2 = ttl * jml
          this.pakets[idx].jmlitem = ttl
          this.pakets[idx].total = ttl2
          this.pakets.pop()
        }
      })
    },
    edit(item) {
      this.updateSubmit = true
      this.dtupdate = Object.assign({}, item)
      this.dtpack.id = this.dtupdate.id
      this.dtpack.namapaket = this.dtupdate.namapaket
      this.dtpack.jmlitem = this.dtupdate.jmlitem
      this.dtpack.expired_date = this.dtupdate.expired_date
      this.dtpack.deskripsi = this.dtupdate.deskripsi
      this.dtpack.hargaitem = this.dtupdate.hargaitem
    },

  },
  mounted() {
    this.load()
    this.loadpack()
  }
}
</script>
<!-- pp -->
