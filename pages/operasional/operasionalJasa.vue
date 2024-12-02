<template>
  <div>
    <v-container>
      <h1>Operasional Jasa</h1>
      <v-form ref="formatas">
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field id="namatoko" @keydown.enter="namatoko" v-model="all.namatoko" label="Nama Toko"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field id="referensi" @keydown.enter="referensi" v-model="all.referensi"
              label="No referensi"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field id="date" v-model="all.date" label="Date"></v-text-field>
          </v-col>
        </v-row>
      </v-form>
      <v-row v-show="isTable">
        <v-col cols="12" md="12">
          <v-simple-table>
            <template>
              <thead>
                <tr>
                  <th>No.</th>
                  <th>Nama Produk</th>
                  <th>Harga</th>
                  <th>Jumlah</th>
                  <th>Total</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, i) in dtsementara" :key="item.id">
                  <td>{{ i + 1 }}</td>
                  <td>{{ item.namaproduk }}</td>
                  <td>{{ item.harga }}</td>
                  <td>{{ item.jumlah }}</td>
                  <td>{{ item.total }}</td>
                  <td>
                    <v-btn @click="hapusbrg(item.index)" class="mx-2" fab dark small color="primary">
                      <v-icon dark>
                        mdi-minus
                      </v-icon>
                    </v-btn>
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-col>
      </v-row>
      <v-form ref="formbawah">
        <v-row>
          <v-col cols="12" md="6">
            <v-text-field id="namaproduk" @keydown.enter="namaproduk" v-model="all.namaproduk"
              label="Nama Produk"></v-text-field>
          </v-col>
          <v-col cols="12" md="3">
            <v-text-field id="harga" @keydown.enter="harga" v-model="all.harga" label="Harga"></v-text-field>
          </v-col>
          <v-col cols="12" md="3">
            <v-text-field id="jumlah" @keydown.enter="jumlah" v-model="all.jumlah" label="Jumlah"></v-text-field>
          </v-col>
        </v-row>
      </v-form>
    </v-container>
    <div>
      <v-data-table :headers="hdopr" :items="dtopr" :items-per-page="5" class="elevation-1">
      </v-data-table>
    </div>
    <div class="text-end d-flex flex-row-reverse mb-6 bg-surface-variant mt-7" cols="12" ms="6" md="6">
      <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" @click="save" :disabled="dialog"
        :loading="dialog">Save</v-btn>
      <v-btn class="mr-4" color="success" width="150px" @click="baru">New</v-btn>
    </div>
    <div class="text-center">
      <v-dialog v-model="dialog" hide-overlay persistent width="170">
        <v-card color="#90A4AE" dark>
          <v-card-text><br>
            <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import Swal from 'sweetalert2'
export default {
  data() {
    return {
      hdopr: [
        { text: "No", value: "id" },
        { text: "Product Name", value: "nama" },
        { text: "Price", value: "harga" },
        { text: "Qty", value: "jumlah" },
        { text: "Total", value: "total" },
      ],
      all: {
        namatoko: "",
        referensi: "",
        date: "",
        namaproduk: "",
        harga: "",
        jumlah: ""
      },
      dtopr: [],
      dtsementara: [],
      isTable: false,
      dialog: false
    }
  },
  watch: {
    dialog(val) {
      if (!val) return
      setTimeout(() => (this.dialog = false), 2000)
    },
  },
  methods: {
    save() {
      Swal.fire({
        title: 'Pastikan data yang diisi benar',
        text: 'Anda Yakin?',
        showCancelButton: true,
        confirmButtonText: 'Yes',
      }).then((result) => {
        if (result.isConfirmed) {
          this.dialog = true
          let objsv = { datasv: this.dtsementara }
          this.apiurl = process.env.API_URL
          axios
            .post(this.apiurl + '/save/operasionaltreat', objsv)
            .then(() => {
              this.load()
              this.isTable = false
              this.$refs.formatas.reset()
              this.$refs.formbawah.reset()
              var sv = this.dtsementara.length
              this.dtsementara.splice(0, sv)
              Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'Berhasil menyimpan data',
                showConfirmButton: false,
                timer: 1000
              })
            })
        }
      })
    },
    hapusbrg(index) {
      let idx = this.dtsementara.findIndex(x => x.index === index)
      this.dtsementara.splice(idx, 1)
    },
    jumlah() {
      this.isTable = true
      let idx = this.dtsementara.length
      let ttl = (this.all.harga * this.all.jumlah)
      var objek = {
        index: idx,
        namatoko: this.all.namatoko,
        referensi: this.all.referensi,
        date: this.all.date,
        namaproduk: this.all.namaproduk,
        harga: this.all.harga,
        jumlah: this.all.jumlah,
        total: ttl
      }
      this.dtsementara.push(objek)
      this.$refs.formbawah.reset()
      document.getElementById('namaproduk').focus()
    },
    namatoko() {
      document.getElementById('referensi').focus()
    },
    referensi() {
      document.getElementById('namaproduk').focus()
    },
    namaproduk() {
      document.getElementById('harga').focus()
    },
    harga() {
      document.getElementById('jumlah').focus()
    },
    baru() {
      this.isTable = false
      var nw = this.dtsementara.length
      this.dtsementara.splice(0, nw)
      document.getElementById('namatoko').focus()
      this.$refs.formatas.reset()
      this.$refs.formbawah.reset()
      var date = new Date();
      var tahun = date.getFullYear();
      var bulan = date.getMonth();
      var tanggal = date.getDate();
      switch (bulan) {
        case 0: bulan = "01"; break;
        case 1: bulan = "02"; break;
        case 2: bulan = "03"; break;
        case 3: bulan = "04"; break;
        case 4: bulan = "05"; break;
        case 5: bulan = "06"; break;
        case 6: bulan = "07"; break;
        case 7: bulan = "08"; break;
        case 8: bulan = "09"; break;
        case 9: bulan = "10"; break;
        case 10: bulan = "11"; break;
        case 11: bulan = "12"; break;
      }
      if (tanggal == 1) {
        tanggal = "0" + tanggal
      } else if (tanggal == 2) {
        tanggal = "0" + tanggal
      } else if (tanggal == 3) {
        tanggal = "0" + tanggal
      } else if (tanggal == 4) {
        tanggal = "0" + tanggal
      } else if (tanggal == 5) {
        tanggal = "0" + tanggal
      } else if (tanggal == 6) {
        tanggal = "0" + tanggal
      } else if (tanggal == 7) {
        tanggal = "0" + tanggal
      } else if (tanggal == 8) {
        tanggal = "0" + tanggal
      } else if (tanggal == 9) {
        tanggal = "0" + tanggal
      } else {
        tanggal = tanggal
      }
      this.all.date = tahun + "-" + bulan + "-" + tanggal
    },
    load() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/show/opjasa/Jasa')
        .then((ress) => {
          this.dtopr = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    }
  },
  mounted() {
    this.load()
  }
}
</script>
