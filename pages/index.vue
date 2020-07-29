<template>
  <div class="container  mt-5 p-4 m-6">
    <v-row justify="left">
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-dialog v-model="dialog" persistent max-width="600px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark v-bind="attrs" v-on="on">
              Tambah Data
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{formTitle}}</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-card class="mx-auto" max-width="90%" color="white" outlined>
                  <div class="input">
                    <div class="text-field mx-auto" style="width:90%">
                        <v-text-field label="Nama Barang" v-model="editedItem.nama_barang" :rules="nama_rules" hide-details="auto" required></v-text-field>
                    </div>
                    <div class="text-field mx-auto" style="width:90%">
                      <v-select
                        class="my-2"
                        :items="list_kategori"
                        item-text="nama_kategori"
                        item-value="id_kategori"
                        v-model="editedItem.d_kategori"
                        label="Kategori"
                        return-object
                      ></v-select>
                    </div>
                    <div class="text-field mx-auto" style="width:90%">
                        <v-text-field label="Harga" v-model="editedItem.harga" :rules="harga_rules" hide-details="auto" required></v-text-field>
                    </div>
                  </div>
                </v-card>   
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn>
              <v-btn color="blue darken-1" @click="validate">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-form>
    </v-row>
    
    <!-- <v-card class="mx-auto" max-width="90%" color="white" outlined>
      <div class="input">
        <div class="text-field mx-auto" style="width:90%">
            <v-text-field label="Nama Barang" v-model="nama_barang" :rules="nama_rules" hide-details="auto"></v-text-field>
        </div>
        <div class="text-field mx-auto" style="width:90%">
          <v-select
            class="my-2"
            :items="list_kategori"
            item-text="nama_kategori"
            item-value="id_kategori"
            v-model="d_kategori"
            label="Kategori"
            return-object
          ></v-select>
        </div>
        <div class="text-field mx-auto" style="width:90%">
            <v-text-field label="Harga" v-model="harga" :rules="harga_rules" hide-details="auto"></v-text-field>
        </div>
        <div class="tambah absolute my-3">
          <v-btn class="button my-3 ml-12" style="width:90%;" @click="save" color="success">Tambah</v-btn>
        </div>
      </div>
    </v-card> -->
    <!-- <div class="cari">
      <v-flex md4>
        <v-text-field ali
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-flex>
    </div> -->
    <div class="tabel mx-auto mt-6 pt-4" style="width:100%;">
      <v-row no-gutters mb="12">
        <v-col sm="1">
          <v-card class="pa-2" outlined tile>
            <th width="10%">No.</th>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile>
            <th width="30%">Nama Barang</th>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile>
            <th width="30%">Kategori Barang</th>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile>
            <th width="30%">Harga</th>
          </v-card>
        </v-col>
        <v-col sm="2">
          <v-card class="pa-2" outlined tile>
            <th width="30%">Aksi</th>
          </v-card>
        </v-col>
      </v-row>
      <v-row v-for="(l, index) in List" :key="index" no-gutters mb="12">
        <v-col sm="1">
          <v-card class="pa-2" outlined tile>      <!-- </tr> -->
            <td>{{index+1}}</td>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile>
            <td class="nama text-left">{{l.nama_barang}}</td>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile>
            <td class="kategori text-left">{{l.nama_kategori}}</td>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile>
            <td class="harga text-left">Rp. {{l.harga}}</td>
          </v-card>
        </v-col>
        <v-col sm="2">
          <v-card class="pa-2" outlined tile>
            <v-row justify="center">
              <v-btn class="btn-actions" x-small color="warning" dark
                @click="editItem(l)">
                <v-icon small>
                  mdi-pencil
                </v-icon>
              </v-btn>
              <v-btn class="btn-actions" x-small color="error" dark
                @click="deleteItem(l)">
                <v-icon small>
                  mdi-delete
                </v-icon>
              </v-btn>
            </v-row>
          </v-card>
        </v-col>
      </v-row>
    </div>
  </div>
</template>

<script>

  export default {
    data () {
      return {
        // headers: [
        //   {
        //     text: 'no',
        //     align: 'start',
        //     sortable: false,
        //     value: 'no',
        //   },
        //   { text: 'Nama Barang', value: 'nama_barang', sortable: true, class:'th-head'},
        //   { text: 'Kategori', value: 'nama_kategori', sortable: true, class:'th-head'},
        //   { text: 'Harga', value: 'harga', sortable: true, class:'th-head'},
        //   { text: 'Aksi', align: 'center', value: 'actions', sortable: false, class:'th-head'},
        // ],
        List:[],
        nama_rules: [
          value => !!value || 'Required.',
          value => (value && value.length >= 3) || 'Min 3 characters',
        ],
        harga_rules: [
          value => !!value || 'Required.',
          value => (value && value.length >= 3) || 'Min 3 characters',
        ],
        d_kategori: [],
        dialog:false,
        list_kategori: [],
        editedIndex: -1,
        editedItem:{
          nama_barang:'',
          d_kategori:'',
          harga:'',
        }
      }
    },
    created () {
      this.getData()
      this.getDataKategori()
    },
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Tambah Barang' : 'Edit Barang'
      }
    },
    methods: {
      getData () {
        this.$axios.$get('barang')
        .then(response => {
            this.List = response.data
            console.log('LIST Data',this.List)
          })
        .catch(error => console.log(error))
      },

      editItem (item) {
        this.editedIndex = this.List.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.editedItem.id = item.id
        this.editedItem.d_kategori = item.id_kategori
        this.editedItem.nama_barang = item.nama_barang
        this.editedItem.harga = item.harga
        // this.editedItem.nama_barang = this.List.find(e => e.id_barang === this.editedItem.id_barang) 
        // console.log('idnya',item.id_kategori)
        // this.editedItem.id_kategori = this.List.find(e => e.id_kategori === this.editedItem.id_kategori)                  
        this.dialog = true
      },

      getDataKategori (id_kategori) {
        this.$axios.$get('kategori')
        .then(response => {
            this.list_kategori = response.data
            console.log('Data',this.list_kategori)
          })
        .catch(error => console.log(error))
      },
      validate () {
        if (this.$refs.form.validate()) {
          if(this.valid){
            this.save()
            console.log("Validasi", this.valid);
          }
        } else {
          console.log('none !')
        }
      },
      reset () {
        this.$refs.form.reset()
      },

      save () {
        // this.editedItem.d_kategori = this.d_kategori.find(e => e.id_kategori === this.editedItem.d_kategori.id_kategori)                  
         this.editedItem.id = this.editedItem.id
        if (this.editedIndex > -1) {
          let tableItem = this.editedItem
          Object.assign(this.List[this.editedIndex], this.editedItem)
          return this.$axios
          .$put("barang/" + this.editedItem.id,tableItem)
          
          .then(response => {
            this.x = response;
            this.getData();
            this.dialog = false;
          })
          console.log('item',this.editedItem.id)
          .catch(error => {
            console.log(error)
            this.getData()
          })
        } else {
          let tableItem = this.editedItem
          this.List.push(this.editedItem)
          this.$axios.$post("barang", tableItem)
          .then(response => {
            console.log(response)
            this.getData();
            this.dialog = false;
          })
          .catch(error => {
            console.log(error)
            this.getData();
          });
          // this.$axios.$post("sekolahs", tableItem)
          // .then(response => {
          //   console.log(response)
          //   this.swalMessage('Data Sekolah Berhasil Disimpan')
          //   this.getData();
          //   this.dialog = false;
          // })
          // .catch(error => {
          //   console.log(error)
          //   this.getData();
          // });
        }
      // this.close() 
      },

      // save () {
      //   // let tableItem = this.editedItem
      //   // this.List.push(this.editedItem)
      //   this.$axios.$post("barang", {
      //     nama_barang: this.nama_barang,
      //     id_kategori: this.d_kategori.id_kategori,
      //     harga: this.harga
      //   })
      //   .then(response => {
      //     console.log(response)
      //     this.getData();
      //     this.dialog = false;
      //   })
      //   .catch(error => {
      //     console.log(error)
      //     this.getData();
      //   });
      // }
    },
  }
</script>