<template>
  <div>
    <h1>Penjualan Data Medis</h1>
    <br />
    <v-data-table
      :headers="headerdatamedis"
      :items="users"
      :search="cari"
      :items-per-page="5"
      class="elevation-1"
    >
    </v-data-table>
    <v-col
      class="text-and text-end d-flex flex-row-reverse bg-surface-variant mt-5"
    >
      <v-btn @click="exportexel">| Export to Excel |</v-btn>
      <v-btn @click="exportpdf">| Export to PDF</v-btn>
      <v-btn @click="cetak">| Print</v-btn>
    </v-col>
  </div>
</template>
<script>
import axios from 'axios'
export default {
  // name="layoutProducts",
  data() {
    return {
      judulhalaman: 'Data Medis',
      headerdatamedis: [
        { text: 'No', value: 'id' },
        { text: 'Name', value: 'namaproduk' },
        { text: 'Selling Price', value: 'hargajual' },
        { text: 'Tanggal Jual', value: 'tgljual' },
        { text: 'No Faktur', value: 'nofaktur' },
      ],
      cari: '',
      damed: {
        namaproduk: '',
        hargajual: '',
        kodegrup: 'M',
        deskripsi: '',
      },
      users: [],
    }
  },
  methods: {
    load() {
      this.apiurl = process.env.API_URL
      axios
        .get(this.apiurl + '/produks/index')
        .then((response) => {
          this.users = response.data
          console.log(response.data)
        })
        .catch(() => {
          console.log(err)
        })
    },
    cetak() {
      this.apiurl = process.env.API_URL
      let uri = this.apiurl + '/penjualmedis/print'
      window.open(uri, '_blank', 'width=800,height=600,left=300,top=200')
    },
    exportpdf() {
      let uri = this.apiurl + '/penjualmedis/pdf'
      window.open(uri, 'width=800,height=600,left=300,top=200')
    },
    exportexel() {
      let uri = this.apiurl + '/penjualmedis/excel'
      window.open(uri, 'width=800,height=600,left=300,top=200')
    },
  },
  mounted() {
    this.load()
  },
}
</script>
//pp
