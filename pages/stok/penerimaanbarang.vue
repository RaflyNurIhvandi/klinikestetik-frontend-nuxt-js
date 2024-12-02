<template>
  <div>
    <v-container>
      <h1>Penerimaan Barang</h1>
      <v-form ref="formatas">
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.kodepemasok" id="kodepemasok" label="Stok / Pemasok"
              @keydown.enter="kodepemasok"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.noreferensi" id="referensi" label="No referensi"
              @keydown.enter="referensi"></v-text-field>
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
            <v-text-field v-model="all.tanggal" id="tanggal" label="Date" @keypress="isNumber($event)"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.keterangan" id="keterangan" label="Keterangan"
              @keydown.enter="keterangan"></v-text-field>
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
                  <th>Kode Barang</th>
                  <th>Nama Barang</th>
                  <th>Harga</th>
                  <th>Jumlah</th>
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
            <v-text-field v-model="all.kodeproduk" id="kodeproduk" label="Kode Produk"
              @keydown.enter="kodeproduk"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.namaproduk" id="namaproduk" label="Nama Produk" disabled></v-text-field>
          </v-col>
          <v-col cols="12" md="3">
            <v-text-field v-model="all.harga" id="harga" label="Harga" @keydown.enter="harga" @keypress="isNumber($event)"></v-text-field>
          </v-col>
          <v-col cols="12" md="3">
            <v-text-field v-model="all.jumlah" id="jumlah" label="Jumlah" @keydown.enter="jumlah"
              v-show="!isJumlah" @keypress="isNumber($event)"></v-text-field>
            <v-text-field v-model="all.jumlah" id="jumlahedit" label="Jumlah" v-show="isJumlah" @keypress="isNumber($event)"></v-text-field>
          </v-col>
        </v-row>
      </v-form>
      <v-row>
        <v-col class="d-flex flex-row mb-6 bg-surface-variant mt-5" cols="12" ms="6" md="6">
          <v-btn class="mr-4" color="success" width="150px" @click="baru">New</v-btn>
          <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="!updateSubmit"
            @click="save" :disabled="dialog" :loading="dialog">Save</v-btn>
          <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="updateSubmit"
            v-on:click="update(all)">Save</v-btn>
          <v-btn style="color: white;" color="orange" class="mr-4" width="150px" @click="print">Print</v-btn>
        </v-col>
      </v-row>
    </v-container>
    <div>
      <v-data-table :headers="hdtrma" :items="dtpenerimaan" :items-per-page="5" class="elevation-1">
        <template v-slot:[`item.aksi`]="{ item }">
          <v-icon color="error" @click="edit(item)">
            mdi-pencil-box-multiple-outline
          </v-icon>
        </template>
      </v-data-table>
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
    <div class="text-center">
      <v-dialog v-model="isPrint" width="auto">
        <v-card>
          <div>
            <v-data-table :headers="hdprint" :items="dtprint" :items-per-page="3" class="elevation-1">
              <template v-slot:[`item.aksi`]="{ item }">
                <v-icon color="error" @click="getPrint(item)">
                  mdi-printer
                </v-icon>
              </template>
            </v-data-table>
          </div>
          <div class="d-flex flex-row-reverse bg-surface-variant">
            <v-btn class="ma-2 pa-2" width="10px" color="error">
              <v-icon @click="closeprint">
                mdi-close
              </v-icon>
            </v-btn>
          </div>
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
      hdprint: [
        { text: "Nama Pemasok", value: "terimadari" },
        { text: "No Terima", value: "noterima" },
        { text: "", value: "aksi" }
      ],
      hdtrma: [
        { text: "No", value: "id" },
        { text: "Product Name", value: "namaproduk" },
        { text: "Price", value: "harga" },
        { text: "Qty", value: "jumlah" },
        { text: "Total", value: "total" },
        { text: "Tools", value: "aksi" },
      ],
      all: {
        id: "",
        noterima: "",
        namapemasok: "",
        kodepemasok: "",
        noreferensi: "",
        tanggal: "",
        keterangan: "",
        kodeproduk: "",
        namaproduk: "",
        harga: "",
        jumlah: "",
      },
      dtedit: {
        id: "",
        noterima: "",
        kodeproduk: "",
        namaproduk: "",
        harga: "",
        jumlah: "",
        total: ""
      },
      isKodeenter: false,
      datapemasok: [],
      dataproduk: [],
      dtsementara: [],
      dtpenerimaan: [],
      dtpemasokpenerimaan: [],
      dtprint:[],
      editjumlahdisabled: 1,
      isvalid: false,
      isTable: false,
      updateSubmit: false,
      isJumlah: false,
      dialog: false,
      isPrint: false
    }
  },
  computed: {
    filterPemasok: function () {
      return this.datapemasok.filter((pm) => {
        return pm.kodepemasok.match(this.all.kodepemasok)
      })
    },
    filterProduk: function () {
      return this.dataproduk.filter((pr) => {
        return pr.kodeproduk.match(this.all.kodeproduk)
      })
    },
    filterEdit: function () {
      return this.dtpemasokpenerimaan.filter((fe) => {
        return fe.noterima.match(this.all.noterima)
      })
    },
    filterPemasokEdit: function () {
      return this.datapemasok.filter((fpe) => {
        return fpe.namapemasok.match(this.all.namapemasok)
      })
    }
  },
  watch: {
    dialog(val) {
      if (!val) return
      setTimeout(() => (this.dialog = false), 2000)
    },
  },
  methods: {
    getPrint(item) {
      this.apiurl = process.env.API_URL
      window.open(this.apiurl + '/cetak/print/penerimaan/' + item.noterima, "cetak", "width=800,height=800,left=300,top=200");
    },
    closeprint() {
      this.isPrint = false
    },
    print() {
      this.loadprint()
      this.isPrint = true
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
    update(all) {
      Swal.fire({
        title: 'Pastikan data yang diisi benar',
        text: 'Anda Yakin?',
        showCancelButton: true,
        confirmButtonText: 'Yes',
      }).then((result) => {
        if (result.isConfirmed) {
          this.dialog = true
          this.apiurl = process.env.API_URL
          axios
            .put(this.apiurl + '/update/penerimaandetails/' + all.id, {
              'kodeproduk': this.all.kodeproduk,
              'namaproduk': this.all.namaproduk,
              'harga': this.all.harga,
              'jumlah': this.all.jumlah,
              'total': this.all.harga * this.all.jumlah
            })
          axios
            .put(this.apiurl + '/update/penerimaanbarangs/' + all.noterima, {
              'tanggal': this.all.tanggal,
              'referensi': this.all.noreferensi,
              'terimadari': this.all.namapemasok,
              'keterangan': this.all.keterangan
            })
            .then(() => {
              this.loadprint()
              this.loadpenerimaan()
              this.loadDataPemasok()
              this.$refs.formatas.reset()
              this.$refs.formbawah.reset()
              this.updateSubmit = false
              Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'Berhasil mengupdate data',
                showConfirmButton: false,
                timer: 1000
              })
            })
        }
      })
    },
    lihatpemasok() {
      this.filterEdit.forEach((lp) => {
        this.all.namapemasok = lp.terimadari
        this.all.noreferensi = lp.referensi
        this.all.tanggal = lp.tglterima
        this.all.keterangan = lp.keterangan
      })
      this.filterPemasokEdit.forEach((fpe) => {
        this.all.kodepemasok = fpe.kodepemasok
      })
    },
    edit(item) {
      this.isJumlah = true
      this.isKodeenter = true
      this.updateSubmit = true
      this.dtedit = Object.assign({}, item)
      this.all.id = this.dtedit.id
      this.all.kodeproduk = this.dtedit.kodeproduk
      this.all.namaproduk = this.dtedit.namaproduk
      this.all.harga = this.dtedit.harga
      this.all.jumlah = this.dtedit.jumlah
      this.all.noterima = this.dtedit.noterima
      this.lihatpemasok()
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
            .post(this.apiurl + '/simpan/stok', obj)
            .then(() => {
              this.loadprint()
              this.loadpenerimaan()
              this.loadDataPemasok()
              this.$refs.formatas.reset()
              this.$refs.formbawah.reset()
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
    jumlah() {
      this.isTable = true
      let idx = this.dtsementara.length
      let ttl = (this.all.harga * this.all.jumlah)
      var objek = {
        index: idx,
        terimadari: this.all.namapemasok,
        referensi: this.all.noreferensi,
        tglterima: this.all.tanggal,
        keterangan: this.all.keterangan,
        kodeproduk: this.all.kodeproduk,
        namaproduk: this.all.namaproduk,
        harga: this.all.harga,
        jumlah: this.all.jumlah,
        total: ttl
      }
      this.dtsementara.push(objek)
      this.$refs.formbawah.reset()
      document.getElementById('kodeproduk').focus()
    },
    harga() {
      if (this.isJumlah == true) {
        document.getElementById('jumlahedit').focus()
      } else {
        document.getElementById('jumlah').focus()
      }
    },
    kodeproduk() {
      this.filterProduk.forEach((el) => {
        this.all.namaproduk = el.namaproduk
        this.all.harga = el.hargabeli
        document.getElementById('harga').focus()
      })
    },
    keterangan() {
      document.getElementById('kodeproduk').focus()
    },
    referensi() {
      document.getElementById('keterangan').focus()
    },
    kodepemasok() {
      this.isKodeenter = true
      document.getElementById('referensi').focus()
      this.filterPemasok.forEach((ps) => {
        this.all.namapemasok = ps.namapemasok
      })
    },
    loadprint() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/print/peneriman')
        .then((ress) => {
          this.dtprint = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    loadDataPemasok() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/show/datapemasok')
        .then((ress) => {
          this.dtpemasokpenerimaan = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    loadpenerimaan() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/show/penerimaan')
        .then((ress) => {
          this.dtpenerimaan = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    loadproduk() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/kodepro')
        .then((ress) => {
          this.dataproduk = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    loadpemasok() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/kodepm')
        .then((ress) => {
          this.datapemasok = ress.data
        })
        .catch(() => {
          console.log(err)
        })
    },
    baru() {
      this.loadpemasok()
      this.loadproduk()
      var del = this.dtsementara.length
      this.dtsementara.splice(0, del)
      this.isTable = false
      this.isJumlah = false
      this.updateSubmit = false
      this.$refs.formatas.reset()
      this.$refs.formbawah.reset()
      document.getElementById('kodepemasok').focus()
      this.isKodeenter = false
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
      this.all.tanggal = tahun + "-" + bulan + "-" + tanggal + " " + jam + ":" + menit + ":" + detik
    },
  },
  mounted() {
    this.loadpemasok()
    this.loadproduk()
    this.loadpenerimaan()
    this.loadDataPemasok()
    this.loadprint()
  }
}
</script>
