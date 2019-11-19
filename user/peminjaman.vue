<template>
    <section class="section">
          <h1 class="section-header">
            <div>Data Peminjaman</div>
          </h1>
          <div class="row">
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-primary">
                  <i class="ion ion-ios-paper-outline"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Total List</h4>
                  </div>
                  <div class="card-body">
                    {{rows}}
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-danger">
                  <i class="ion ion-person"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Buku Dipinjam</h4>
                  </div>
                  <div class="card-body">
                    {{sumdipinjam}}
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-warning">
                  <i class="ion ion-paper-airplane"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Total Stok</h4>
                  </div>
                  <div class="card-body">
                    {{sum}}
                  </div>
                </div>
              </div>
            </div>                
          </div>
          <h1 class="section-header">
            <div>Data Peminjaman</div>
          </h1>
          <div class="section-body">
            <div class="row">
              <div class="col-12 col-md-12 col-lg-12">
                <div class="card">
                  <div class="card-header">
                    <h4>Data Buku</h4>
                  <b-card
                    class="mt-2">
                      
                      <b-form-input v-model="search" placeholder="Pencarian..." class="mb-3" v-on:keyup.enter="searching"></b-form-input>
                      <b-table striped hover :items="list_pinjam" :fields="fields">
                        <template v-slot:cell(Aksi)="data">
                          <b-button variant="primary" size="sm" v-on:click="kembali(data.item.id)">
                              Kembali
                          </b-button>
                        </template>
                      </b-table>
                      <b-pagination
                        v-model="currentPage"
                        :per-page="perPage"
                        :total-rows="rows"
                        align="center"
                        v-on:input="pagination">
                      </b-pagination>

                      <b-toast id="loadingToast" title="Processing Data" no-auto-hide>
                        <b-spinner label="Spinning" variant="success"></b-spinner>
                        <strong class="text-success">Loading...</strong>
                      </b-toast>

                      <b-toast id="message" title="Message">
                        <strong class="text-success">{{ message }}</strong>
                      </b-toast>
                  </b-card>
                </div>
              </div>
            </div>
          </div>
        </section>
</template>
<script>
module.exports = {
  data : function(){
    return {
      tanggal_kembali: "",
      peminjam: "",
      jumlah_pinjam: "",
      search: "",
      id: "",
      title: "",
      idbuku: "",
      name: "",
      jumlah_pinjam: "",
      status: "",
      denda: "",
      stok: "",
      dipinjam: "",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 5,
      key: "",
      list_pinjam: [],
      book: [],
      peminj: [],
      user: [],
      sum: "",
      sumdipinjam: "",
      // total: "",
      fields: ["id", "idpeminjam", "name", "idbuku", "jumlah_pinjam","tanggal_pinjam", "tanggal_kembali",  "status", "denda", "Aksi"],
    }
  },
  methods: {
    getData : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      axios.get(base_url + "/pinjam/" +  this.perPage + "/" + offset, conf)
      .then(response => {
        if(response.data.status == 1){
          this.$bvToast.hide("loadingToast");
          this.list_pinjam = response.data.pinjam;
          this.rows = response.data.count;
        } else {
          window.location = "../login.html";
        }
        
      })
      .catch(error => {
        console.log(error);
      });
    },
    getBook : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      axios.get(base_url + "/book/" + this.perPage + "/" + offset, conf)
      .then(response => {
        if(response.data.status == 1){
          this.$bvToast.hide("loadingToast");
          this.sum = response.data.sum;
          this.sumdipinjam = response.data.sumdipinjam;
        } else {
          window.location = "../login.html";
        }
        
      })
      .catch(error => {
        console.log(error);
      });
    },
    pagination : function(){
      if(this.search == ""){
        this.getData();
      } else {
        this.searching();
      }
    },
  kembali : function(id){
      let conf = { headers: { "Authorization" : "Bearer " + this.key} };
      if(confirm('Apakah anda yakin ingin menghapus data ini?')){
        this.$bvToast.show("loadingToast");
        axios.delete(base_url + "/kembali/" + id, conf)
        .then(response => {
            this.getData();
            this.$bvToast.hide("loadingToast");
            this.message = response.data.message;
            this.$bvToast.show("message");
        })
        .catch(error => {
          console.log(error);
        });
      }
      
    },
  },
  mounted(){
    this.key = this.$cookies.get("Authorization");
    this.getData();
    this.getBook();
  }
}
</script>
