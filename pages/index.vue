<template>
  <div>
    <div class="mt-5">
      <div class="row">
        <div>
          <apexchart type="pie" width="380" :options="produkoption" :series="grafikproduk"></apexchart>
        </div>
        <div id="chart">
          <apexchart type="line" width="380" :options="penjualanoption" :series="grafikpenjualan"></apexchart>
        </div>
      </div>
    </div>
    <button @click="tes()">Tes</button>
  </div>
</template>

<script>
import VueApexCharts from 'vue-apexcharts'
import Swal from 'sweetalert2'
export default {
  name: 'IndexPage',
  components: {
    apexcharts: VueApexCharts,
    apexchart: VueApexCharts,
  },
  data() {
    return {
      apiurl: '',
      produkoption: {
        chart: {
          type: 'pie',
        },
        title: {
          text: 'Jumlah Produk'
        },
        labels: ['Jumlah barang', 'Jumlah barang medis', 'Jumlah paket promo', 'Treatment'],
      },
      grafikproduk: [],
      penjualanoption: {
        chart: {
          height: 200,
          type: 'line',
          zoom: {
            enabled: false
          }
        },
        dataLabels: {
          enabled: false
        },
        stroke: {
          curve: 'straight'
        },
        title: {
          text: 'Penjualan tahun 2023',
          align: 'left'
        },
        grid: {
          row: {
            colors: ['#f3f3f3', 'transparent'], // takes an array which will be repeated on columns
            opacity: 1
          },
        },
        xaxis: {
          categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep'],
        }
      },
      grafikpenjualan: [{
          name:'penjualan',
          data: []
        }],
    }
  },
  methods: {
    tes(){
      Swal.fire(
      'The Internet?',
      'That thing is still around?',
      'question'
    )
    },
    getjmlhbrgB() {
      let apiurl = process.env.API_URL
      this.$axios.get(this.apiurl + '/dashboard/getjumlahbrgb')
        .then((res) => {
          this.grafikproduk.push(res.data)
          this.$nextTick(() => {
            this.getjmlhbrgM();
            this.getjmlhbrgpp();
            this.getjmlhbrgt();
          })
          console.log(this.grafikpenjualan[0].data)
        })
    },
    getjmlhbrgM() {
      this.$axios.get(this.apiurl + '/dashboard/getjumlahbrgm')
        .then((res) => {
          this.grafikproduk.push(res.data)
        })
    },
    getjmlhbrgpp() {
      this.$axios.get(this.apiurl + '/dashboard/getjumlahbrgpp')
        .then((res) => {
          this.grafikproduk.push(res.data)
        })
    },
    getjmlhbrgt() {
      this.$axios.get(this.apiurl + '/dashboard/getjumlahbrgpp')
        .then((res) => {
          this.grafikproduk.push(res.data)
        })
    },
    getjmlpenjualan1(){
      this.$axios.get(this.apiurl + '/dashboard/getpenjualan1')
      .then((res)=>{
        var data = res.data
        this.grafikpenjualan[0].data.push(data)
        this.$nextTick(()=>{
          this.getjmlpenjualan2();
          this.getjmlpenjualan3();
          this.getjmlpenjualan4();
          this.getjmlpenjualan5();
        })
      })
    },
    getjmlpenjualan2(){
      this.$axios.get(this.apiurl + '/dashboard/getpenjualan2')
      .then((res)=>{
        var data = res.data
        this.grafikpenjualan[0].data.push(data)
      })
    },
    getjmlpenjualan3(){
      this.$axios.get(this.apiurl + '/dashboard/getpenjualan3')
      .then((res)=>{
        var data = res.data
        this.grafikpenjualan[0].data.push(data)
      })
    },
    getjmlpenjualan4(){
      this.$axios.get(this.apiurl + '/dashboard/getpenjualan4')
      .then((res)=>{
        var data = res.data
        this.grafikpenjualan[0].data.push(data)
      })
    },
    getjmlpenjualan5(){
      this.$axios.get(this.apiurl + '/dashboard/getpenjualan5')
      .then((res)=>{
        var data = res.data
        this.grafikpenjualan[0].data.push(data)
      })
    },

  },
  mounted() {
    this.getjmlhbrgB();
    this.getjmlpenjualan1();
  },
}
</script>
