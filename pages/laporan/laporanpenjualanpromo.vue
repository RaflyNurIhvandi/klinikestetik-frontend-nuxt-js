<template>
  <div>
    <h1 class="mb-4">Penjualan Paket Promo</h1>
    <v-data-table :headers="pjpro" :items="dtpro" :search="cari" :items-per-page="5" class="elevation-1">
    </v-data-table>

    <!-- tombol -->
    <div class="d-flex justify-sm-center mt-7">
      <v-btn @click="getPrint()" width="175px" color="primary">Print</v-btn>
      <v-btn @click="getExcel()" class="ms-5" width="175px" color="success">Export to excel</v-btn>
      <v-btn @click="getPdf()" class="ms-5" width="175px" color="error">Export to pdf</v-btn>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default{
  data(){
    return{
      pjpro:[
        {text: "No", value: "id"},
        {text: "Kode Produk", value: "kodeproduk"},
        {text: "Nama Produk", value: "namaproduk"},
        {text: "Price", value: "hargajual"},
        {text: "Qty", value: "stokmin"},
        {text: "Tanggal Jual", value: "tgljual"},
        {text: "Nomor Faktur", value: "nofaktur"},
      ],
      dtpro:[]
    }
  },
  methods:{
    load(){
      this.apiurl = process.env.API_URL
      axios
      .get(this.apiurl+'/lappro')
      .then((ress)=>{
        this.dtpro = ress.data
        console.log(ress.data)
      })
      .catch(()=>{
        console.log(err)
      })
    },
    getPrint() {
      this.apiurl = process.env.API_URL
      let uri = this.apiurl + '/print/promo';
      window.open(uri, "_blank", "width=800,height=600,left=300,top=200");
    },
    getExcel() {
      this.apiurl = process.env.API_URL
      let uri = this.apiurl + '/excel/promo';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
    getPdf() {
      this.apiurl = process.env.API_URL
      let uri = this.apiurl + '/pdf/promo';
      window.open(uri, "_self", "width=800,height=600,left=300,top=200");
    },
  },
  mounted(){
    this.load()
  }
}
</script>
<!-- pp -->
