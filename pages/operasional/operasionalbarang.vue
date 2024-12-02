<template>
    <div>
        <h1>Operasional Barang</h1><br><br>
            <v-form ref="dataatas">
        <v-row>
        <v-col cols="12" md="4">
          <v-text-field v-model="form.namatoko"  id="tap1" @keydown.enter="fil1" :rules="namaRules" label="Name Toko" required></v-text-field>
        </v-col>
        <v-col cols="12"  md="4"></v-col>
        <v-col cols="12" md="4">
          <v-text-field  v-model="form.noreferensi" id="tap2" @keydown.enter="fil2" :rules="nolRules" label="No Referensi" @keypress="isNumber($event)"  required></v-text-field>
        </v-col>
      </v-row>  
      <v-row>
        <v-col cols="12" md="4"></v-col>

        <v-col cols="12" md="4"></v-col>

        <v-col cols="12" md="4">
          <v-text-field v-model="form.tgl" id="tap3" @keydown.enter="fil3" :rules="tglRules" label="Date" required></v-text-field>
        </v-col>
      </v-row>
      </v-form>
      <v-row v-show="noTable">
      <v-col cols="12" md="12">
        <v-simple-table>
          <template>
            <thead>
              <tr>
                <th>No</th>
                <th>Name produk</th>
                <th>Harga</th>
                <th>Jumlah</th>
                <th>Total</th>
                <th>Tools</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, i) in dtsem" :key="item.id">
                <td>{{ i +1 }}</td>
                <td>{{ item.nama }}</td>
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
      <v-form ref="databawah">
        <v-row>
          <v-col cols="12"  md="6">
            <v-text-field v-model="form.nama" id="nama" @keydown.enter="fil4" :rules="namaRules" label="Name Produk" required></v-text-field>
          </v-col>
          <v-col cols="12" md="3">
            <v-text-field v-model="form.harga" id="tap5" @keydown.enter="fil5" :rules="hargaRules" label="Harga"  @keypress="isNumber($event)" required></v-text-field>
          </v-col>
          <v-col cols="12" md="3">
            <v-text-field v-model="form.jumlah" id="tap6" @keydown.enter="fil6" :rules="jumlahRules" label="Jumlah" @keypress="isNumber($event)" required ></v-text-field>
          </v-col>
        </v-row><br>
      </v-form> 
      <div>
        <v-data-table :headers="opbarang" :items="froms" :search="cari" :items-per-page="5" class="elevation-1">
        </v-data-table>
            <v-row>
              <v-col class="text-and text-end d-flex flex-row-reverse bg-surface-variant mt-5" cols="12" ms="12" md="12" >
              <v-btn class="mr-4" width="150px" color="primary" name="button" @click="save" v-show="!updateSubmit">SAVE</v-btn>
              <v-btn class="mr-4" color="success" width="150px" @click="data">NEW</v-btn>
              </v-col>
          </v-row>
      </div> 
  </div>
</template>

<script>
    import axios from 'axios';
    import Swal from "sweetalert2";
export default {
  data() {
    return {
      apiurl: "",
      opbarang: [
        { text: 'No', value: 'id' },
        { text: 'Product Name', value: 'nama' },
        { text: 'Price', value: 'harga' },
        { text: 'Qty', value: 'jumlah' },
        { text: 'Total', value:'total' },
      ],
      date:"",
      cari: '',
      apiurl: '',
      form: {
        namatoko:"" ,
        noreferensi:"" ,
        tgl:"" ,
        nama:"" ,
        harga:"",
        jumlah: "",
      },
      froms: [],
      dtsem: [],
      noTable:false,
      updateSubmit: false,
      isvalid: false,
    }
  },
  methods: {
    hapusbrg(index) {
      let idx = this.dtsem.findIndex(x => x.index === index)
      this.dtsem.splice(idx, 1);
    },
    data() {
      this.$refs.dataatas.reset()
      this.$refs.databawah.reset()
      document.getElementById('tap1').focus()
      var date = new Date();
      var tahun = date.getFullYear();
      var bulan = date.getMonth();
      var tanggal = date.getDate();
      switch(bulan) {
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
      if(tanggal == 1){
        tanggal = "0" + tanggal
      }else if(tanggal == 2){
        tanggal = "0" + tanggal
      }else if(tanggal == 3){
        tanggal = "0" + tanggal
      }else if(tanggal == 4){
        tanggal = "0" + tanggal
      }else if(tanggal == 5){
        tanggal = "0" + tanggal
      }else if(tanggal == 6){
        tanggal = "0" + tanggal
      }else if(tanggal == 7){
        tanggal = "0" + tanggal
      }else if(tanggal == 8){
        tanggal = "0" + tanggal
      }else if(tanggal == 9){
        tanggal = "0" + tanggal
      }
      this.form.tgl=tahun + "-" + bulan + "-" + tanggal
    },
    fil1() {
       document.getElementById('tap2').focus()
    },
    fil2() {
      document.getElementById('nama').focus()
    },
    fil4() {
        document.getElementById('tap5').focus()
    },
    fil5() {
        document.getElementById('tap6').focus()
    },
    fil6() {
      this.noTable = true
      let id = this.dtsem.length
      let tl = (this.form.harga * this.form.jumlah)
      var objek = {
            index: id,
            namatoko: this.form.namatoko,
            noreferensi: this.form.noreferensi,
            tgl: this.form.tgl,
            nama: this.form.nama,
            harga: this.form.harga,
            jumlah: this.form.jumlah,
            total: tl 
      }
      this.dtsem.push(objek)
      this.$refs.databawah.reset()
      document.getElementById('nama').focus()
    },
    load() {
        this.apiurl = process.env.API_URL
        axios
        .get(this.apiurl + '/operasionals/index/Barang')
            .then((ress) => {
                this.froms = ress.data
            })
            .catch(() => {
             console.log(err)
        })
    },
    save() {
      Swal.fire({
          title: 'Pastikan data yang diisi benar',
          text: 'anda yakin?',
          showCancelButton: true,
          confirmButtonText: 'Yes',
        }).then((result) => {
          if (result.isConfirmed) {
            let obj ={data1: this.dtsem}
            this.apiurl = process.env.API_URL
            axios.post(this.apiurl + '/operasionals/save', obj)
              .then(() => {
                this.load()
                this.$refs.dataatas.reset()
                this.$refs.databawah.reset()
                var dt = this.dtsem.length
                this.dtsem.splice(0,dt)
                this.noTable = false
                Swal.fire({
                  position: 'top-end',
                  icon: 'success',
                  title: 'Berasil Menyimpan Data',
                  showConfirmButton: false,
                  timer: 1000
                })
              })
                .catch(() => {
                  // console.log(err)
             })
         }
      })
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
    },
    mounted() {
      this.load()
  }
}
</script>
