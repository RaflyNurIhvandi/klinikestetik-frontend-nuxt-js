<template>
  <div>
    <v-container>
      <v-form ref="formatas">
        <h2>Pencatatan Pembelian Barang</h2>
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.pemasok" id="pemasok" @keydown.enter="pemasok" label="Pemasok"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.invoice" id="invoice" @keydown.enter="invoice" label="Invoice No"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
            <div v-for="kod in filterPemasok" :key="kod.id" v-show="isKodeenter">
              <h2 style="font-weight:normal;">{{ kod.namapemasok }}</h2>
              <h5 style="font-weight:normal;">{{ kod.alamatpemasok }}<br>{{ kod.kotapemasok }} Telp. {{ kod.notelppemasok
              }}</h5>
            </div>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.date" id="date" @keydown.enter="date" label="Date"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="8">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.terms" id="terms" @keydown.enter="terms" label="Terms"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
            <div>
              <v-btn class="mr-4" color="success" width="150px" @click="baru">New Invoice</v-btn>
              <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="!updateSubmit"
                @click="save" :disabled="dialog" :loading="dialog">Save</v-btn>
            </div>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.payment" id="payment" @keydown.enter="payment"
              label="Payment Method"></v-text-field>
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
                  <th>Kode Produk</th>
                  <th>Nama Produk</th>
                  <th>Harga</th>
                  <th>Jumlah</th>
                  <th>Diskon</th>
                  <th>Total</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, i) in dtsementara" :key="item.id">
                  <td>{{ i + 1 }}</td>
                  <td>{{ item.kodeproduk }}</td>
                  <td>{{ item.namaproduk }}</td>
                  <td>{{ item.harga }}</td>
                  <td>{{ item.jumlah }}</td>
                  <td>{{ item.diskon }}</td>
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
          <v-col cols="12" md="2">
            <v-text-field v-model="all.kodeproduk" id="kodeproduk" @keydown.enter="kodeproduk"
              label="Kode Produk"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.namaproduk" id="namaproduk" @keydown.enter="namaproduk" label="Nama Produk"
              disabled></v-text-field>
          </v-col>
          <v-col cols="12" md="2">
            <v-text-field v-model="all.harga" id="harga" @keydown.enter="harga" label="Harga"></v-text-field>
          </v-col>
          <v-col cols="12" md="2">
            <v-text-field v-model="all.jumlah" id="jumlah" @keydown.enter="jumlah" label="Jumlah"></v-text-field>
          </v-col>
          <v-col cols="12" md="2">
            <v-text-field v-model="all.diskon" id="diskon" @keydown.enter="diskon" label="Diskon"></v-text-field>
          </v-col>
        </v-row>
      </v-form>
      <div>
        <v-data-table :headers="hdcatat" :items="datapembeliandetails" :items-per-page="5" class="elevation-1">
          <template v-slot:[`item.aksi`]="{ item }">
            <v-icon color="error" @click="edit(item)">
              mdi-pencil-box-multiple-outline
            </v-icon>
          </template>
        </v-data-table>
      </div>
      <v-form ref="formtotal">
        <v-row>
          <v-col cols="12" md="9">
          </v-col>
          <v-col cols="12" md="3" class="mt-6">
            <v-text-field id="grandtotal" v-model="all.grandtotal" label="Grand Total"></v-text-field>
          </v-col>
        </v-row>
      </v-form>
    </v-container>
    <div class="text-center">
      <v-dialog v-model="dialog" hide-overlay persistent width="170">
        <v-card color="#90A4AE" dark>
          <v-card-text><br>
            <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
    {{ dtsementara }}
  </div>
</template>
<script>
import axios from 'axios'
import Swal from 'sweetalert2'
export default {
  data() {
    return {
      hdcatat: [
        { text: "No", value: "id" },
        { text: "Product Name", value: "namaproduk" },
        { text: "Price", value: "harga" },
        { text: "Qty", value: "jumlah" },
        { text: "Discount", value: "diskon" },
        { text: "Total", value: "total" },
        { text: "", value: "aksi" },
      ],
      all: {
        id: "",
        pemasok: "",
        invoice: "",
        date: "",
        terms: "",
        payment: "",
        kodeproduk: "",
        namaproduk: "",
        harga: "",
        jumlah: "",
        diskon: "",
        grandtotal: "",
      },
      isKodeenter: false,
      datapemasok: [],
      dataproduk: [],
      isTable: false,
      dtsementara: [],
      dttotal: [],
      dtgrdttl: [],
      dialog: false,
      datapembeliandetails: []
    }
  },
  computed: {
    filterPemasok: function () {
      return this.datapemasok.filter((pm) => {
        return pm.kodepemasok.match(this.all.pemasok)
      })
    },
    filterProduk: function () {
      return this.dataproduk.filter((pr) => {
        return pr.kodeproduk.match(this.all.kodeproduk)
      })
    },
  },
  watch: {
    dialog(val) {
      if (!val) return
      setTimeout(() => (this.dialog = false), 2000)
    },
  },
  methods: {
    pushgrand() {
      // var pg = this.dtgrdttl
      // this.dtsementara.push(pg)
      let idx = this.dtsementara.length
      let ttl = (this.all.harga * this.all.jumlah)
      var objek = {
        index: idx,
        kodepemasok: this.all.pemasok,
        terimadari: this.all.namapemasok,
        infoice: this.all.invoice,
        tglbeli: this.all.date,
        terms: this.all.terms,
        payment: this.all.payment,
        kodeproduk: this.all.kodeproduk,
        namaproduk: this.all.namaproduk,
        harga: this.all.harga,
        jumlah: this.all.jumlah,
        diskon: this.all.diskon,
        total: ttl,
        grandtotal : this.dtgrdttl
      }
      this.dtsementara.push(objek)
    },
    save() {
      Swal.fire({
        title: 'Pastikan data yang diisi benar',
        text: 'Anda Yakin?',
        showCancelButton: true,
        confirmButtonText: 'Yes',
      }).then((result) => {
        if (result.isConfirmed) {
          this.dialog = true
          let obj = { data1: this.dtsementara }
          this.apiurl = process.env.API_URL
          axios
            .post(this.apiurl + '/simpan/pembelian', obj)
            .then(() => {
              this.loadpembeliandetails()
              this.loadPemasok()
              this.$refs.formatas.reset()
              this.$refs.formbawah.reset()
              this.$refs.formtotal.reset()
              this.isTable = false
              var delt = this.dtsementara.length
              this.dtsementara.splice(0, delt)
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
    total() {
      let ttl = (this.all.harga * this.all.jumlah)
      this.dttotal.push(ttl)
      var grdtl = this.dttotal
      var total = 0
      for (let i = 0; i < grdtl.length; i++) {
        total += grdtl[i];
      }
      this.all.grandtotal = total
      var gt = this.all.grandtotal
      this.dtgrdttl = gt
      // this.dtgrdttl.splice(0, 1, gt)
    },
    diskon() {
      this.isTable = true
      let idx = this.dtsementara.length
      let ttl = (this.all.harga * this.all.jumlah)
      var objek = {
        index: idx,
        kodepemasok: this.all.pemasok,
        terimadari: this.all.namapemasok,
        infoice: this.all.invoice,
        tglbeli: this.all.date,
        terms: this.all.terms,
        payment: this.all.payment,
        kodeproduk: this.all.kodeproduk,
        namaproduk: this.all.namaproduk,
        harga: this.all.harga,
        jumlah: this.all.jumlah,
        diskon: this.all.diskon,
        total: ttl
      }
      var rrr = this.dtsementara.length - 1
      this.dtsementara.splice(rrr, 1, objek)
      this.total()
      document.getElementById('kodeproduk').focus()
      this.$refs.formbawah.reset()
      this.pushgrand()
    },
    jumlah() {
      document.getElementById('diskon').focus()
    },
    harga() {
      document.getElementById('jumlah').focus()
    },
    kodeproduk() {
      this.filterProduk.forEach((fp) => {
        this.all.namaproduk = fp.namaproduk
        this.all.harga = fp.hargabeli
      })
      document.getElementById('harga').focus()
    },
    payment() {
      document.getElementById('kodeproduk').focus()
    },
    terms() {
      document.getElementById('payment').focus()
    },
    invoice() {
      document.getElementById('terms').focus()
    },
    pemasok() {
      this.isKodeenter = true
      document.getElementById('invoice').focus()
    },
    loadpembeliandetails() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/loadpembelian')
        .then((ress) => {
          this.datapembeliandetails = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    loadProduk() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/loadfilterproduk')
        .then((ress) => {
          this.dataproduk = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    loadPemasok() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/loadpemasok')
        .then((ress) => {
          this.datapemasok = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    baru() {
      var dt = this.dtsementara.length
      var dttl = this.dttotal.length
      this.dtsementara.splice(0, dt)
      this.dttotal.splice(0, dttl)
      this.isTable = false
      this.isKodeenter = false
      this.$refs.formatas.reset()
      this.$refs.formbawah.reset()
      this.$refs.formtotal.reset()
      document.getElementById('pemasok').focus()
      var date = new Date();
      var tahun = date.getFullYear();
      var bulan = date.getMonth();
      var tanggal = date.getDate();
      var jam = date.getHours();
      var menit = date.getMinutes();
      var detik = date.getSeconds();
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
      if (jam == 0) {
        jam = "0" + jam
      } else if (jam == 1) {
        jam = "0" + jam
      } else if (jam == 2) {
        jam = "0" + jam
      } else if (jam == 3) {
        jam = "0" + jam
      } else if (jam == 4) {
        jam = "0" + jam
      } else if (jam == 5) {
        jam = "0" + jam
      } else if (jam == 6) {
        jam = "0" + jam
      } else if (jam == 7) {
        jam = "0" + jam
      } else if (jam == 8) {
        jam = "0" + jam
      } else if (jam == 9) {
        jam = "0" + jam
      } else {
        jam = jam
      }
      if (menit == 0) {
        menit = "0" + menit
      } else if (menit == 1) {
        menit = "0" + menit
      } else if (menit == 2) {
        menit = "0" + menit
      } else if (menit == 3) {
        menit = "0" + menit
      } else if (menit == 4) {
        menit = "0" + menit
      } else if (menit == 5) {
        menit = "0" + menit
      } else if (menit == 6) {
        menit = "0" + menit
      } else if (menit == 7) {
        menit = "0" + menit
      } else if (tanggal == 8) {
        menit = "0" + menit
      } else if (menit == 9) {
        menit = "0" + menit
      } else {
        menit = menit
      }
      if (detik == 0) {
        detik = "0" + detik
      } else if (detik == 1) {
        detik = "0" + detik
      } else if (detik == 2) {
        detik = "0" + detik
      } else if (detik == 3) {
        detik = "0" + detik
      } else if (detik == 4) {
        detik = "0" + detik
      } else if (detik == 5) {
        detik = "0" + detik
      } else if (detik == 6) {
        detik = "0" + detik
      } else if (detik == 7) {
        detik = "0" + detik
      } else if (detik == 8) {
        detik = "0" + detik
      } else if (detik == 9) {
        detik = "0" + detik
      } else {
        detik = detik
      }
      this.all.date = tahun + "-" + bulan + "-" + tanggal + " " + jam + ":" + menit + ":" + detik
    }
  },
  mounted() {
    this.loadPemasok()
    this.loadProduk()
    this.loadpembeliandetails()
  }
}
</script>
