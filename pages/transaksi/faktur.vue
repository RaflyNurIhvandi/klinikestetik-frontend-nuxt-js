<template>
  <div>
    <h1>faktur</h1>
    <div class="row">
      <div class="col">
        <v-col>
          <v-row>
            <v-text-field ref="kodepelanggan" v-model="all.kodepelanggan" @keydown.enter="getpelanggan"
              label="Kode Pelanggan" required append-icon="mdi-magnify" @click:append="modalcaripelanggan"></v-text-field>
          </v-row>
          <div>
            <v-row>
              <v-text-field v-model='item.namapelanggan' label="Nama Pelanggan" disabled></v-text-field>
            </v-row>
            <v-row>
              <v-text-field v-model='item.alamatpelanggan' label="Alamat Pelanggan" disabled></v-text-field>
            </v-row>
            <v-row>
              <v-text-field v-model='item.kotapelanggan' label="Kota Pelanggan" disabled></v-text-field>
            </v-row>
            <v-row>
              <v-text-field v-model='item.notelppelanggan' label="No. Telp. Pelanggan" disabled></v-text-field>
            </v-row>
          </div>
        </v-col>
      </div>
      <div class="col">

      </div>
      <div class="col">
        <v-col>
          <v-row>
            <v-text-field v-model="all.nofaktur" label="No. Faktur"></v-text-field>
          </v-row>
          <v-row>
            <v-text-field v-model="all.tgljual" type="date" label="Tanggal Jual"></v-text-field>
          </v-row>
          <v-row>
            <v-text-field v-model="all.syaratbayar" label="Syarat Bayar"></v-text-field>
          </v-row>
          <v-row>
            <v-text-field v-model="all.tgljatuhtempo" type="date" label="Tanggal jatuh tempo"></v-text-field>
          </v-row>
          <v-row>
            <v-text-field v-model="all.keteranganbayar" label="Keterangan Bayar"></v-text-field>
          </v-row>
        </v-col>
      </div>
    </div>
    <!-- modal cari pelanggan-->
    <div class="text-center">
      <v-dialog v-model="findcust" width="auto">
        <v-card>
          <v-card-title>
            Cari Customer
            <v-spacer></v-spacer>
            <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line
              hide-details></v-text-field>
          </v-card-title>
          <v-data-table :headers="headers" :items="cust" :search="search">
            <template v-slot:item.aksi="{ item }">
              <v-btn color="blue" @click="pilihcust(item)">
                Pilih
              </v-btn>
            </template>
          </v-data-table>
        </v-card>
      </v-dialog>
    </div>
    <!-- Modal cari barang -->
    <div class="text-center">
      <v-dialog v-model="findbrg" width="auto">
        <v-card>
          <v-card-title>
            Cari Barang
            <v-spacer></v-spacer>
            <v-text-field v-model="searchbrg" append-icon="mdi-magnify" label="Search" single-line
              hide-details></v-text-field>
          </v-card-title>
          <v-data-table :headers="headerbarang" :items="brg" :search="searchbrg">
            <template v-slot:item.aksi="{ item }">
              <v-btn color="blue" @click="pilihbrg(item)">
                Pilih
              </v-btn>
            </template>
          </v-data-table>
        </v-card>
      </v-dialog>
    </div>
    <!-- tabel transaksi -->
    <v-container>
      <v-simple-table>
        <template>
          <thead>
            <tr>
              <th>No.</th>
              <th>Kode Barang</th>
              <th>Nama Barang</th>
              <th>Harga</th>
              <th>Jumlah</th>
              <th>Diskon</th>
              <th>Total</th>
              <th>ACT</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, i) in kdbrg" :key="item.id">
              <td>{{ i + 1 }}</td>
              <td>{{ item.kodeproduk }}</td>
              <td>{{ item.namaproduk }}</td>
              <td><input type="text" :value="item.hargajual" disabled></td>
              <td>
                <input ref="jumlahbarang" type="text" v-model="item.jumlah" :disabled="editjumlahdisabled == 1"
                  @change="updatebrg(item.index, item.kodeproduk, item.hargajual, item.jumlah, item.diskon, item.total)">
              </td>
              <td>{{ item.diskon }}</td>
              <td><input type="text" v-model="item.total" disabled></td>
              <td><v-btn @click="editbrg" class="mx-2" fab dark small color="cyan">
                  <v-icon dark>
                    mdi-pencil
                  </v-icon>
                </v-btn>
                <v-btn @click="hapusbrg(item.index)" class="mx-2" fab dark small color="primary">
                  <v-icon dark>
                    mdi-minus
                  </v-icon>
                </v-btn>
              </td>
            </tr>

            <tr>
              <td></td>
              <td>
                <v-text-field ref="kdbarang" label="" v-model="barang.kodeproduk" @keydown.enter="getbrg"
                  append-icon="mdi-magnify" @click:append="modalcaribarang"></v-text-field>
              </td>
              <td>
                <v-text-field label="" v-model="barang.namaproduk" disabled></v-text-field>
              </td>
              <td>
                <v-text-field label="" v-model="barang.hargajual" disabled></v-text-field>
              </td>
              <td>
                <v-text-field ref="jmlbarang" label="" v-model="barang.jumlah" @keydown.enter="gettotal"
                  type="number"></v-text-field>
              </td>
              <td>
                <v-text-field ref="dsknbarang" label="" v-model="barang.diskon" @keydown.enter="additem"
                  type="number"></v-text-field>
              </td>
              <td>
                <v-text-field label="" v-model="barang.total" disabled></v-text-field>
              </td>
            </tr>

          </tbody>
        </template>
      </v-simple-table>
    </v-container>
    {{ trblg }}
    <!-- keterangan terbilang dkk -->
    <v-container class="d-flex justify-space-around">
      <v-col cols="6" class="mr-5">
        <v-row cols="12" md="4">
          <v-text-field ref="ktrngn" v-model="all.keterangan" label="Keterangan" @keydown.enter="nexttodiskon"
            outlined></v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-textarea type="text" :value="terbilang" outlined label="Terbilang" disabled></v-textarea>
        </v-row>
        <v-row>
          <v-btn @click="savefaktur">Simpan</v-btn>
          <v-btn @click="cetak">Cetak</v-btn>
        </v-row>
      </v-col>
      <v-spacer></v-spacer>
      <v-col cols="6">
        <v-row cols="12" md="4">
          <v-text-field ref="dskn" v-model="all.diskon" label="Diskon" @focus="cekpassworddiskon">
            <v-btn>asd</v-btn>
            <v-icon slot="append" color="red">
              mdi-key
            </v-icon>
          </v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-text-field ref="vcr" v-model="all.voucher" label="Voucher" outlined
            @keydown.enter="nexttobayar"></v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-text-field disabled :value="gtotal1" label="Total" outlined></v-text-field>
          <v-text-field v-show="fieldhiden" v-model="all.total" label="Total"></v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-text-field disabled :value="totalpjk" label="Total Pajak" outlined></v-text-field>
          <v-text-field v-show="fieldhiden" v-model="all.totalpajak" label="Total Pajak"></v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-text-field disabled :value="gtotal2" label="Grandtotal" outlined></v-text-field>
          <v-text-field v-show="fieldhiden" type="number" v-model="all.grandtotal" label="Grandtotal"
            ref="gtot"></v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-text-field v-model="bayar" label="Bayar" ref="byar" outlined @keydown.enter="simpanbayar"></v-text-field>
          <v-text-field v-show="!gtb" v-model="all.bayar" outlined></v-text-field>
        </v-row>
        <v-row cols="12" md="4">
          <v-text-field disabled :value="kembali" label="Kembali" outlined></v-text-field>
          <v-text-field v-show="fieldhiden" v-model="all.kembali" label="Kembali" outlined></v-text-field>
        </v-row>
      </v-col>
    </v-container>
    <!-- Loading  -->
    <div class="text-center">
      <v-dialog v-model="dialog2" :scrim="false" persistent width="auto">
        <v-card color="primary">
          <v-card-text>
            Finding Data, Please Wait . . . .
            <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
    <div class="text-center">
      <v-dialog v-model="dialog3" :scrim="false" persistent width="auto">
        <v-card color="primary">
          <v-card-text>
            Uploading, Please Wait . . . .
            <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
    {{ autorupiah }}
  </div>
</template>
<script>
import Swal from 'sweetalert2'
export default {
  data() {
    return {
      headerbarang: [
        { text: 'Kode', value: 'kodeproduk' },
        { text: 'Nama Barang', value: 'namaproduk', },
        { text: 'Alamat', value: 'hargajual' },
        { text: 'Pilih', value: 'aksi' },
      ],
      brg: [],
      headers: [
        {
          text: 'Nama Pelanggan',
          align: 'start',
          sortable: false,
          value: 'namapelanggan',
        },
        { text: 'Kode', value: 'kodepelanggan' },
        { text: 'Alamat', value: 'alamatpelanggan' },
        { text: 'No Telp', value: 'notelppelanggan' },
        { text: 'Pilih', value: 'aksi' },
      ],
      cust: [],
      item: {
        namapelanggan: "",
        kotapelanggan: "",
        alamatpelanggan: "",
        notelppelanggan: ""
      },
      all: {
        nofaktur: "",
        tgljual: "",
        kodepelanggan: "",
        syaratbayar: "",
        tgljatuhtempo: "",
        keteranganbayar: "",
        diskon: "",
        voucher: 0,
        total: 0,
        totalpajak: 0,
        grandtotal: 0,
        bayar: "",
        kembali: 0,
        keterangan: "",
      },
      terbilang: "",
      apiurl: '',
      pelanggan: "",
      barang: {
        kodeproduk: "",
        namaproduk: "",
        hargajual: "",
        jumlah: "",
        diskon: "",
        total: "",
      },
      kdbrg: [],
      editjumlahdisabled: 1,
      fieldhiden: false,
      bayar: "",
      search: "",
      searchbrg: "",
      gtb: true,
      proses: false,
      dialog2: false,
      dialog3: false,
      findcust: false,
      findbrg: false
    }
  },
  watch: {
    dialog2(val) {
      if (!val) return
      setTimeout(() => (this.dialog2 = false), 2000)
    },
    dialog3(val) {
      if (!val) return
      setTimeout(() => (this.dialog2 = false), 2000)
    },
  },
  computed: {
    gtotal1() {
      let gt1 = this.kdbrg.reduce((acc, item) => acc + item.total, 0);
      let vcr = this.all.voucher
      let dskn = this.all.diskon
      let ttl = gt1 - dskn - vcr
      this.all.total = ttl

      const rupiah = (number) => {
        return new Intl.NumberFormat("id-ID", {
          style: "currency",
          currency: "IDR"
        }).format(number);
      }
      return rupiah(ttl)
    },
    gtotal2() {
      let ttl = this.all.total
      let ttlpjk = this.all.totalpajak
      let gt = ttl + ttlpjk

      const rupiah = (number) => {
        return new Intl.NumberFormat("id-ID", {
          style: "currency",
          currency: "IDR"
        }).format(number);
      }

      this.all.grandtotal = gt
      return rupiah(gt)
    },
    totalpjk() {
      let ttlpjk = this.all.grandtotal * 11 / 100

      const rupiah = (number) => {
        return new Intl.NumberFormat("id-ID", {
          style: "currency",
          currency: "IDR"
        }).format(number);
      }

      this.all.totalpajak = Math.round(ttlpjk)
      return rupiah(ttlpjk)
    },
    kembali() {
      let zero = null
      let kmbl = this.all.bayar - this.all.grandtotal

      const rupiah = (number) => {
        return new Intl.NumberFormat("id-ID", {
          style: "currency",
          currency: "IDR"
        }).format(number);
      }

      this.all.kembali = kmbl
      if (this.all.bayar == 0) {
        this.all.kembali = zero
        return rupiah(zero)
      } else
        if (this.all.bayar != "0") {
          return rupiah(kmbl)
        }
    },
    trblg() {
      this.terbilang = terbilang(this.all.grandtotal)
      function terbilang(bilangan) {
        bilangan = String(bilangan);
        let angka = new Array('0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0');
        let kata = new Array('', 'Satu', 'Dua', 'Tiga', 'Empat', 'Lima', 'Enam', 'Tujuh', 'Delapan', 'Sembilan');
        let tingkat = new Array('', 'Ribu', 'Juta', 'Milyar', 'Triliun');

        let panjang_bilangan = bilangan.length;
        let kalimat = subkalimat = kata1 = kata2 = kata3 = "";
        let i = j = 0;

        /* pengujian panjang bilangan */
        if (panjang_bilangan > 15) {
          kalimat = "Diluar Batas";
          return kalimat;
        }

        /* mengambil angka-angka yang ada dalam bilangan, dimasukkan ke dalam array */
        for (i = 1; i <= panjang_bilangan; i++) {
          angka[i] = bilangan.substr(-(i), 1);
        }

        i = 1;
        var j = 0;
        kalimat = "";

        /* mulai proses iterasi terhadap array angka */
        while (i <= panjang_bilangan) {

          var subkalimat = "";
          var kata1 = "";
          var kata2 = "";
          var kata3 = "";

          /* untuk Ratusan */
          if (angka[i + 2] != "0") {
            if (angka[i + 2] == "1") {
              kata1 = "Seratus";
            } else {
              kata1 = kata[angka[i + 2]] + " Ratus";
            }
          }

          /* untuk Puluhan atau Belasan */
          if (angka[i + 1] != "0") {
            if (angka[i + 1] == "1") {
              if (angka[i] == "0") {
                kata2 = "Sepuluh";
              } else if (angka[i] == "1") {
                kata2 = "Sebelas";
              } else {
                kata2 = kata[angka[i]] + " Belas";
              }
            } else {
              kata2 = kata[angka[i + 1]] + " Puluh";
            }
          }

          /* untuk Satuan */
          if (angka[i] != "0") {
            if (angka[i + 1] != "1") {
              kata3 = kata[angka[i]];
            }
          }

          /* pengujian angka apakah tidak nol semua, lalu ditambahkan tingkat */
          if ((angka[i] != "0") || (angka[i + 1] != "0") || (angka[i + 2] != "0")) {
            subkalimat = kata1 + " " + kata2 + " " + kata3 + " " + tingkat[j] + " ";
          }

          /* gabungkan variabe sub kalimat (untuk Satu blok 3 angka) ke variabel kalimat */
          kalimat = subkalimat + kalimat;
          i = i + 3;
          j = j + 1;

        }

        /* mengganti Satu Ribu jadi Seribu jika diperlukan */
        if ((angka[5] == "0") && (angka[6] == "0")) {
          kalimat = kalimat.replace("Satu Ribu", "Seribu");
        }

        return (kalimat.trim().replace(/\s{2,}/g, ' ')) + " Rupiah";
      }
    },
    autorupiah() {
      this.bayar = formatRupiah(this.bayar, 'Rp. ')
      this.all.bayar = this.bayar.replace(/[^,\d]/g, '')
      /* Fungsi */
      function formatRupiah(angka, prefix) {
        var number_string = angka.replace(/[^,\d]/g, '').toString(),
          split = number_string.split(','),
          sisa = split[0].length % 3,
          rupiah = split[0].substr(0, sisa),
          ribuan = split[0].substr(sisa).match(/\d{3}/gi);

        if (ribuan) {
          let separator = sisa ? '.' : '';
          rupiah += separator + ribuan.join('.');
        }

        rupiah = split[1] != undefined ? rupiah + ',' + split[1] : rupiah;
        return prefix == undefined ? rupiah : (rupiah ? 'Rp. ' + rupiah : '');
      }
    },
  },
  methods: {
    simpanbayar() {
      let objek = {
        data1: this.kdbrg,
        data2: this.all
      }
      let hasil = '';
      if (this.all.kodepelanggan == "" || this.all.total == "") {
        this.erroralert();
      } else {
        Swal.fire({
          title: 'Anda Yakin?',
          text: "Pastikan Data Sudah Benar",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Ya!'
        })
          .then((result) => {
            if (result.isConfirmed) {
              // this.dialogue();
              this.$nextTick(() => {
                this.savefaktur();
              })
              // this.$nextTick(() => {

              // })
            }
          })
      }
    },
    pilihcust(item) {
      this.all.kodepelanggan = item.kodepelanggan
      this.item.namapelanggan = item.namapelanggan
      this.item.alamatpelanggan = item.alamatpelanggan
      this.item.kotapelanggan = item.kotapelanggan
      this.item.notelppelanggan = item.notelppelanggan
      this.findcust = false
      this.$nextTick(() => {
        this.$refs.kdbarang.focus()
      })

    },
    pilihbrg(item) {
      this.barang.kodeproduk = item.kodeproduk
      this.barang.namaproduk = item.namaproduk
      this.barang.hargajual = item.hargajual
      this.findbrg = false
      this.$nextTick(() => {
        this.$refs.jmlbarang.focus();
      })
    },
    findingbrg() {
      this.$axios.get(this.apiurl + '/faktur/getallbarang')
        .then((res) => {
          this.brg = res.data.data
        })
    },
    findingcust() {
      this.$axios.get(this.apiurl + '/faktur/getallcust')
        .then((res) => {
          this.cust = res.data.data
        })
    },
    modalcaripelanggan() {
      this.findcust = true
    },
    modalcaribarang() {
      this.findbrg = true
    },
    nexttobayar() {
      this.$nextTick(() => {
        this.$refs.byar.focus()
      })
    },
    cekpassworddiskon() {
      let asd = prompt('please enter password', '');

      if (asd == '123') {
        // alert('good')
        let qweq = prompt('masukan diskon', '');
        this.all.diskon = qweq
        this.$refs.vcr.focus();
      } else if (asd != '123') {
        this.$refs.vcr.focus();

      }
    },
    nexttodiskon() {
      this.$refs.dskn.focus()
    },
    cetak() {
      window.open(this.apiurl + '/faktur/cetak/' + this.all.nofaktur, "cetak", "width=800,height=800,left=300,top=200");
    },
    print() {
      window.open(this.apiurl + '/faktur/cetak/' + this.all.nofaktur, "cetak", "width=800,height=800,left=300,top=200");
    },
    savefaktur() {
      let objek = {
        data1: this.kdbrg,
        data2: this.all
      }
      let hasil = '';
      if (this.all.kodepelanggan == 0 || this.all.total == 0) {
        this.erroralert();
      } else {
        Swal.fire({
          title: 'Anda Yakin?',
          text: "Pastikan Data Sudah Benar",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Ya!'
        })
          .then((result) => {
            if (result.isConfirmed) {
              this.dialogue();
              this.$axios.post(this.apiurl + '/faktur/insertitem', objek)
                .then(() =>{
                  // hasil = data.status
                  // if (hasil === 200) {
                  //   this.successalert();
                  // } else {
                  //   this.erroralert();
                  // }
                  this.$nextTick(()=>{
                    let apiurl = process.env.API_URL
                    let all = this.all.nofaktur
                    window.open(apiurl + '/faktur/cetak/' + all, "cetak", "width=800,height=800,left=300,top=200");
                  })
                })
            }
          })
      }
    },
    hapusbrg(index) {
      let idx = this.kdbrg.findIndex(x => x.index === index)
      this.kdbrg.splice(idx, 1);
    },
    editbrg() {
      this.editjumlahdisabled = 0

    },
    updatebrg(index, kodeproduk, hargajual, jumlah, diskon, total) {
      this.editjumlahdisabled = 1
      this.$refs.kdbarang.focus()
      let newtotal = jumlah * hargajual - diskon
      let idxobjx = this.kdbrg.findIndex((x => x.kodeproduk == kodeproduk))
      // this.kdbrg[idxobjx].jumlah = jumlah
      this.kdbrg[idxobjx].total = newtotal
      // this.$nextTick(() => {
      //   this.trblng();
      // })
    },
    load() {
      this.$refs.kodepelanggan.focus()
      this.apiurl = process.env.API_URL
    },
    dialogue() {
      this.dialog2 = true
    },
    async getpelanggan(event) {
      let plgn = event.target.value
      await this.dialogue();
      await this.$axios.get(this.apiurl + '/pelanggan/get/' + plgn)
        .then((res) => {
          let respon = res.status
          if (respon == undefined || res.data.data.length == 0) {
            this.erroralert2();
            this.$refs.kodepelanggan.focus();
          } else if (res.data.data.length == 1) {
            this.dialog2 = false
            this.$nextTick(() => {
              this.$refs.kdbarang.focus()
              this.item.namapelanggan = res.data.data[0].namapelanggan
              this.item.alamatpelanggan = res.data.data[0].alamatpelanggan
              this.item.kotapelanggan = res.data.data[0].kotapelanggan
              this.item.notelppelanggan = res.data.data[0].notelppelanggan
            })
          }
        }).catch((res) => {
          this.erroralert2();
        })
    },
    getbrg(event) {
      let kode = event.target.value
      this.$axios.get(this.apiurl + '/barang/get/' + kode)
        .then((res) => {
          if (res.status == undefined && res.data.data.length == 0) {
            // this.proses = true
            this.erroralert();
          } else {
            this.proses = true
            let namaproduk = res.data.data[0].namaproduk
            let hargajual = res.data.data[0].hargajual
            let kodeproduk = res.data.data[0].kodeproduk
            this.barang.kodeproduk = kodeproduk
            this.barang.namaproduk = namaproduk
            this.barang.hargajual = hargajual
            this.$nextTick(() => {
              // this.proses = false
              this.$refs.jmlbarang.focus()
              this.barang.jumlah = "1"
            })
          }
        })
        .catch((res) => {
          if (res.status == undefined && this.kdbrg.length >= 1) {
            this.$refs.ktrngn.focus();
          } else {
            console.log()
            // this.proses = true
            this.erroralert2();
            this.$nextTick(() => {
              // this.proses = false
            })
          }
        })
    },
    getdate() {
      this.$axios.get(this.apiurl + '/getdate/get')
        .then((res) => {
          this.all.tgljual = res.data
          this.all.tgljatuhtempo = res.data
        })
    },
    gettotal() {
      this.$refs.dsknbarang.focus();
      this.barang.total = this.barang.hargajual * this.barang.jumlah - this.barang.diskon
    },
    pushobject() {
      // ----------------push action-------------------
      let idx = this.kdbrg.length
      let ttl = (this.barang.hargajual * this.barang.jumlah) - this.barang.diskon
      var my_object = {
        index: idx,
        nofaktur: this.all.nofaktur,
        kodeproduk: this.barang.kodeproduk,
        namaproduk: this.barang.namaproduk,
        hargajual: this.barang.hargajual,
        jumlah: this.barang.jumlah,
        diskon: this.barang.diskon,
        total: ttl,
      };
      this.kdbrg.push(my_object)
      this.$refs.kdbarang.focus()
      // --------------------
    },
    additem() {
      this.fieldhiden = false
      if (this.barang.jumlah == "" || this.barang.jumlah == 0) {
        alert('jumlah kosong')
        this.$refs.jmlbarang.focus();
      } else {
        this.pushobject();
        this.$nextTick(() => {
          let d = this.barang.jumlah

          let cariIndex = this.kdbrg.findIndex(brg => brg.kodeproduk === this.barang.kodeproduk); // dapat indexnya
          let a1 = this.kdbrg[cariIndex].jumlah // dapat nilainya
          let a3 = this.kdbrg[cariIndex].hargajual

          let cr = cariIndex
          let lg = this.kdbrg.length - 1

          if (cr == lg) {
            this.kdbrg[cariIndex].jumlah = d
          } else if (lg > cr) {
            let newtotal = parseInt(d) + parseInt(a1)
            this.kdbrg[cariIndex].jumlah = newtotal
            this.kdbrg[cariIndex].total = newtotal * a3 - this.barang.diskon
            this.kdbrg.pop();
          }
          this.cleanfield();

        })
      }

    },
    loadnofaktur() {
      this.$axios.get(this.apiurl + '/nofaktur/get')
        .then((res) => {
          this.all.nofaktur = res.data
        })
    },
    cleanfield() {
      this.barang.kodeproduk = "",
        this.barang.namaproduk = "",
        this.barang.hargajual = "",
        this.barang.jumlah = "",
        this.barang.diskon = "",
        this.barang.total = ""
    },
    successalert() {
      Swal.fire({
        position: 'top-end',
        icon: 'success',
        title: 'Data Tersimpan',
        showConfirmButton: false,
        timer: 1500
      })
    },
    erroralert() {
      Swal.fire({
        icon: "error",
        title: "Oops...",
        text: "Something went wrong!",
        footer: "Data Not Found / Missing"
      });
    },
    erroralert2() {
      let timerInterval
      Swal.fire({
        title: 'Data Not Found !',
        html: 'please check again',
        timer: 1000,
        timerProgressBar: true,
        didOpen: () => {
          Swal.showLoading()
          const b = Swal.getHtmlContainer().querySelector('b')
          timerInterval = setInterval(() => {
            b.textContent = Swal.getTimerLeft()
          }, 100)
        },
        willClose: () => {
          clearInterval(timerInterval)
        }
      }).then((result) => {
        /* Read more about handling dismissals below */
        if (result.dismiss === Swal.DismissReason.timer) {
          console.log('I was closed by the timer')
        }
      })
    }
  },
  mounted() {
    this.load();
    this.loadnofaktur();
    this.getdate();
    this.findingcust();
    this.findingbrg();
  },
}
</script>
