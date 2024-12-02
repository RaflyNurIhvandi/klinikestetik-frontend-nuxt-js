<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" :mini-variant="miniVariant" :clipped="clipped" fixed app color="blue darken-1"
      dark>
      <v-list>
        <v-list-item v-for="(item, i) in items2" :key="i" :to="item.to" router exact>
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <v-list>
        <v-list-group v-for="item in items" :key="item.title" v-model="item.active" :prepend-icon="item.action" no-action
          color="blue lighten-4">
          <template v-slot:activator>
            <v-list-item-content style="color:white;text-decoration: none;">
              <v-list-item-title v-text="item.title"></v-list-item-title>
            </v-list-item-content>
          </template>

          <v-list-item v-for="child in item.items" :key="child.title">
            <v-list-item-content><a :href="child.to" style="color:white;text-decoration: none;">
                <v-list-item-title v-text="child.title"></v-list-item-title></a>
            </v-list-item-content>
          </v-list-item>
        </v-list-group>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app color="blue lighten-5">
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />

      <v-toolbar-title>klinik <b>estetik</b></v-toolbar-title>
      <v-spacer />

      <v-btn icon>
        <v-icon>mdi-account-tie</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-navigation-drawer v-model="rightDrawer" :right="right" temporary fixed>
      <v-list>
        <v-list-item @click.native="right = !right">
          <v-list-item-action>
            <v-icon light> mdi-repeat </v-icon>
          </v-list-item-action>
          <v-list-item-title>Switch drawer (click me)</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-footer :absolute="!fixed" app color="blue lighten-5">
      <span>&copy; {{ new Date().getFullYear() }} Copyright <b><a href="http://kreasindomediapratama.com"
            style="text-decoration:none">Kreasindo Media Pratama</a></b> . All Rights reserved.</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items2: [
        {
          icon: 'mdi-apps',
          title: 'Beranda',
          to: '/',
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'estetik',
      items: [
        {
          action: 'mdi-ticket',
          items: [
            { title: 'Pemasok', to: '/datainduk/pemasok' },
            { title: 'Pegawai', to: '/datainduk/pegawai' },
            { title: 'Pelanggan', to: '/datainduk/pelanggan' },
            { title: 'Dokter, terapis & asisten', to: '/datainduk/dokterterapisasisten' },
            { title: 'Komisi', to: '/datainduk/komisi' },
            { title: 'Barang', to: '/datainduk/barang' },
            { title: 'Jasa', to: '/datainduk/jasa' },
            { title: 'Medis', to: '/datainduk/medis' },
            { title: 'Paket promo', to: '/datainduk/paketpromo' },
            { title: 'Hak akses', to: '/datainduk/hakakses' },
          ],
          active: false,
          title: 'Data Induk',
        },
        {
          action: 'mdi-silverware-fork-knife',
          active: false,
          items: [
            { title: 'Rekam medik', to: '/frontoffice/rekammedik' },
            { title: 'Pernyataan terapis', to: '/pernyataan' },
          ],
          title: 'Front Office',
        },
        {
          action: 'mdi-school',
          items: [
            { title: 'Faktur', to: '/transaksi/faktur' },
            { title: 'Faktur paket promo', to: '/fakturpromo' },
          ],
          title: 'Transaksi',
        },
        {
          action: 'mdi-human-male-female-child',
          items: [
            { title: 'Penjualan barang', to: '/laporanpenjualanbarang' },
            { title: 'Penjualan jasa', to: '/laporanpenjualanjasa' },
            { title: 'Penjualan medis', to: '/laporanpenjualanmedis' },
            { title: 'Penjualan paket promo', to: '/laporanpenjualanpromo' },
          ],
          title: 'Laporan',
        },
        {
          action: 'mdi-bottle-tonic-plus',
          items: [
            { title: 'Komisi dokter, terapis & asisten', to: '/komisidokterdanterapis' },
            { title: 'Komisi paket promo', to: '/komisipaketpromo' },
          ],
          title: 'Keuangan',
        },
        {
          action: 'mdi-briefcase',
          items: [
            { title: 'Operasional barang', to: '/operasionalbarang' },
            { title: 'Operasional jasa', to: '/operasionaljasa' },
            { title: 'Pembelian produk', to: '/pembelianproduk' },
          ],
          title: 'Operasional',
        },
        {
          action: 'mdi-tag',
          items: [
            { title: 'Penerimaan barang', to: '/penerimaanbarang' },
            { title: 'Kartu stok', to: '/kartustok' },
          ],
          title: 'Stok',
        },
      ],
    }
  },
  created() {
  },
  methods: {
  },
}
</script>
