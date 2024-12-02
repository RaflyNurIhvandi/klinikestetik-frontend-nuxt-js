<template>
    <div>
      <div>
        <h2>Laporan Penjualan Jasa/Treatment</h2>
        <v-text-field append-icon="mdi-magnify"  v-model="cari" label="Search Product Name" single-line></v-text-field>
        <v-data-table :headers="headerdatapenjualan" :items="penjualan" :search="cari" :items-per-page="5" class="elevation-1">
        </v-data-table>
      </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      headerdatapenjualan:[
        { text: "No", value: "id" },
        { text: "Name", value: "namaproduk" },
        { text: "Sell Price", value: "hargajual" },
        { text: "Tanggal", value: "tgljual" },
        { text: "No.faktur", value: "nofaktur" }
      ],
      penjualan: [],
      }
    },
    methods: {
    load() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl+'/penjualan')
        .then((response) => {
          this.penjualan = response.data.data
          console.log(response.data)
        })
        .catch(() => {
          console.log(err)
        });
      },
    },
    mounted() {
    this.load();
  },
}

</script>
