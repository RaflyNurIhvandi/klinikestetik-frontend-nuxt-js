<template>
  <div>
    <h1>Komisi</h1>
    <v-form>
      <v-container>
        <v-row v-for="(komisi) in komisi" :key="komisi.id">
          <v-col cols="12" sm="6" md="6">
            <v-text-field label="Name" v-model="komisi.profesi"></v-text-field>
          </v-col>
          <v-col cols="12" sm="6" md="3">
            <v-text-field label="Incentive / Commission" v-model="komisi.komisi"></v-text-field>
          </v-col>
          <v-col cols="12" sm="6" md="3">
            <v-btn  color="primary" @click="apdet(komisi.id, komisi.profesi, komisi.komisi)">Update</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </div>
</template>
<script >
import Swal from 'sweetalert2'

export default {
  name: "DefaultLayout",
  data() {
    return {
      editform: {
        profesi: "",
        insentif: "",
      },
      komisi: [],
      cari: "",
      apiurl: '',
    }
  },
  methods: {

    lD() {
      this.apiurl = process.env.API_URL
      this.$axios.get(this.apiurl + '/komisi/index')
        .then((res) => {
          this.komisi = res.data.data
        }).catch((res) => {
          this.erroralert();
        })
    },
    apdet(id, profesi, komisi) {
      console.log(profesi)
      console.log(komisi)
      this.$axios.put(this.apiurl + '/komisi/update/' + id, {
        'profesi': profesi,
        'komisi': komisi,
      }).then((res) => {
        this.lD();
        this.successalert();
      }).catch((res) => {
        this.erroralert();
      })
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
        footer: ""
      });
    },

  },
  mounted() {
    this.lD();
  }
}
</script>
