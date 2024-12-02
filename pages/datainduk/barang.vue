<template>
  <div>
    <v-container>
      <h1>Data Product</h1>
      <v-form ref="form">
        <v-row>
          <v-col cols="12" md="6">
            <v-text-field v-model="all.productname" id="productname" @keydown.enter="productname"
              label="Product Name"></v-text-field>
          </v-col>
          <v-col cols="12" md="6">
            <v-text-field v-model="all.purchaseprice" id="purchaseprice" @keydown.enter="purchaseprice"
              label="Purchase Price" @keypress="isNumber($event)"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="6">
            <v-text-field v-model="all.sellingprice" id="sellingprice" @keydown.enter="sellingprice" label="Selling Price"
              @keypress="isNumber($event)"></v-text-field>
          </v-col>
          <v-col cols="12" md="6">
            <v-text-field v-model="all.minimumstok" id="minimumstok" @keydown.enter="minimumstok" label="Minimum Stock"
              @keypress="isNumber($event)"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
            <v-combobox v-model="all.category" id="category" @keydown.enter="category" label="Category"
              :items="['B', 'M', 'T']" variant="underlined"></v-combobox>
          </v-col>
          <v-col cols="12" md="4">
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.expireddate" id="expireddate" @keydown.enter="expireddate" label="Expired Date"
              type="date"></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field v-model="all.brand" id="brand" @keydown.enter="brand" label="Brand"></v-text-field>
          </v-col>
          <v-col cols="12" md="8">
            <v-text-field v-model="all.des" id="des" label="Deskripsi"></v-text-field>
          </v-col>
        </v-row>
      </v-form>
      <v-row>
        <v-col class="d-flex flex-row mb-6 bg-surface-variant mt-5" cols="12" ms="6" md="6">
          <v-btn class="mr-4" color="success" width="150px" @click="baru">New Product</v-btn>
          <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="!updateSubmit"
            @click="save" :disabled="dialog" :loading="dialog">Save</v-btn>
          <v-btn class="mr-4" width="150px" color="primary" type="button" name="button" v-show="updateSubmit"
            v-on:click="update(all)" isvalid="true">Save</v-btn>
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
      <v-data-table :headers="headbarang" :items="dtbarang" :search="cari" :items-per-page="5" class="elevation-1">
        <template v-slot:[`item.tools`]="{ item }">
          <v-icon color="success" @click="edit(item)">
            mdi-pencil
          </v-icon>
          <v-icon color="error" @click="hapus(item)">
            mdi-delete
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
  </div>
</template>
<script>
import axios from 'axios'
import Swal from 'sweetalert2'
export default {
  data() {
    return {
      all: {
        id: "",
        purchaseprice: "",
        productname: "",
        sellingprice: "",
        minimumstok: "",
        category: "",
        expireddate: "",
        brand: "",
        des: ""
      },
      dtedit: {
        id: "",
        hargabeli: "",
        namaproduk: "",
        hargajual: "",
        stokmin: "",
        kodegrup: "",
        tglkadaluarsa: "",
        merk: "",
        deskripsi: ""
      },
      headbarang: [
        { text: "No", value: "id" },
        { text: "Batch Number", value: "kodeproduk" },
        { text: "Product Name", value: "namaproduk" },
        { text: "Selling Price", value: "hargajual" },
        { text: "Stock", value: "stokmin" },
        { text: "Tools", value: "tools" }
      ],
      dtbarang: [],
      dialog: false,
      updateSubmit: false,
      cari: ""
    }
  },
  watch: {
    dialog(val) {
      if (!val) return
      setTimeout(() => (this.dialog = false), 1800)
    },
  },
  methods: {
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
      let uri = this.apiurl + '/barang/cetak/B'
      window.open(uri, "_blank", "width=800,height=600,left=300,top=200")
    },
    getExcel() {
      let uri = this.apiurl + '/barang/excel/B'
      window.open(uri, "_self", "width=800,height=600,left=300,top=200")
    },
    getPdf() {
      let uri = this.apiurl + '/barang/pdf/B'
      window.open(uri, "_self", "width=800,height=600,left=300,top=200")
    },
    hapus(item) {
      Swal.fire({
        title: 'Data yang dihapus tidak bisa dikembalikan',
        text: 'anda yakin?',
        showCancelButton: true,
        confirmButtonText: 'Yes',
      }).then((result) => {
        if (result.isConfirmed) {
          this.dialog = true
          this.apiurl = process.env.API_URL
          axios
            .delete(this.apiurl + `/barang/delete/${item.kodeproduk}`)
            .then(() => {
              this.load()
              Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'Berhasil menghapus data',
                showConfirmButton: false,
                timer: 1000
              })
              this.updateSubmit = false
            })
        }
      })
    },
    cencel() {
      this.$refs.form.reset()
    },
    update(all) {
      Swal.fire({
        title: 'Pastikan data yang diisi benar',
        text: 'anda yakin?',
        showCancelButton: true,
        confirmButtonText: 'Yes',
      }).then((result) => {
        if (result.isConfirmed) {
          this.dialog = true
          this.apiurl = process.env.API_URL
          axios
            .put(this.apiurl + '/update/barang/' + all.id, {
              'purchaseprice': this.all.purchaseprice,
              'productname': this.all.productname,
              'sellingprice': this.all.sellingprice,
              'minimumstok': this.all.minimumstok,
              'category': this.all.category,
              'expireddate': this.all.expireddate,
              'brand': this.all.brand,
              'des': this.all.des,
            })
            .then(() => {
              this.load()
              this.$refs.form.reset()
              Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'Berhasil mengupdate data',
                showConfirmButton: false,
                timer: 1000
              })
              this.updateSubmit = false
            })
        }
      })
    },
    edit(item) {
      this.updateSubmit = true
      this.dtedit = Object.assign({}, item)
      this.all.id = this.dtedit.id
      this.all.purchaseprice = this.dtedit.hargabeli
      this.all.productname = this.dtedit.namaproduk
      this.all.sellingprice = this.dtedit.hargajual
      this.all.minimumstok = this.dtedit.stokmin
      this.all.category = this.dtedit.kodegrup
      this.all.expireddate = this.dtedit.tglkadaluarsa
      this.all.brand = this.dtedit.merk
      this.all.des = this.dtedit.deskripsi
    },
    save() {
      if (this.all.productname == null) {
        Swal.fire({
          icon: 'error',
          title: 'Product Name tidak boleh kosong',
          showConfirmButton: false,
          timer: 1000
        })
        document.getElementById('productname').focus()
      } else if (this.all.sellingprice == null) {
        Swal.fire({
          icon: 'error',
          title: 'Selling Price tidak boleh kosong',
          showConfirmButton: false,
          timer: 1000
        })
        document.getElementById('sellingprice').focus()
      } else if (this.all.category == null) {
        Swal.fire({
          icon: 'error',
          title: 'Category tidak boleh kosong',
          showConfirmButton: false,
          timer: 1000
        })
        document.getElementById('category').focus()
      } else {
        Swal.fire({
          title: 'Pastikan data yang diisi benar',
          text: 'anda yakin?',
          showCancelButton: true,
          confirmButtonText: 'Yes',
        }).then((result) => {
          if (result.isConfirmed) {
            this.dialog = true
            this.apiurl = process.env.API_URL
            axios
              .post(this.apiurl + '/produk/save', this.all)
              .then(() => {
                this.load()
                this.$refs.form.reset()
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
      }
    },
    baru() {
      this.updateSubmit = false
      document.getElementById('productname').focus()
      this.$refs.form.reset()
    },
    productname() {
      document.getElementById('purchaseprice').focus()
    },
    purchaseprice() {
      document.getElementById('sellingprice').focus()
    },
    sellingprice() {
      document.getElementById('minimumstok').focus()
    },
    minimumstok() {
      document.getElementById('category').focus()
    },
    category() {
      document.getElementById('expireddate').focus()
    },
    expireddate() {
      document.getElementById('brand').focus()
    },
    brand() {
      document.getElementById('des').focus()
    },
    load() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/produk/B')
        .then((ress) => {
          this.dtbarang = ress.data
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
