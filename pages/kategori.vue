<template>
  <div class="container m-6 mt-5 p-4">
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
                <v-card class="mx-auto" max-width="100%" min-height="75px" color="white" outlined>
                  <div class="input">
                    <div class="row">
                      <div class="text-field mx-auto" style="width:85%">
                        <v-text-field label="Nama Kategori" v-model="editedItem.nama_kategori" :rules="nama_rules" hide-details="auto" required></v-text-field>
                      </div>
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
    <div class="tabel mx-auto mt-6 pt-4" style="width:100%;">
      <v-row no-gutters mb="12">
        <v-col sm="1">
          <v-card class="pa-2" outlined tile>
            <th width="10%">No.</th>
          </v-card>
        </v-col>
        <v-col sm="8">
          <v-card class="pa-2" outlined tile >
            <th width="40%">Nama Kategori</th>
          </v-card>
        </v-col>
        <v-col sm="3">
          <v-card class="pa-2" outlined tile >
            <th width="40%">Aksi</th>
          </v-card>
        </v-col>
      </v-row>
      <v-row v-for="(l, index) in List" :key="index" no-gutters mb="12">
        <v-col sm="1">
          <v-card class="pa-2" outlined tile>
            <td>{{index+1}}</td>
          </v-card>
        </v-col>
        <v-col sm="8">
          <v-card class="pa-2" outlined tile>
            <td class="nama text-left">{{l.nama_kategori}}</td>
          </v-card>
        </v-col>
<!-- update -->
        <v-col sm="3">
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
  import Swal from 'sweetalert'
  import axios from "axios"
  export default {
    data () {
      return {
        List:[],
        nama_rules: [
          value => !!value || 'Required.',
          value => (value && value.length >= 3) || 'Min 3 characters',
        ],
        edit_rules: [
          value => !!value || 'Required.',
          value => (value && value.length >= 3) || 'Min 3 characters',
        ],
        nama_kategori: [],
        dialog:false,
        dial:false,
        editedIndex: -1,
        editedItem:{
          nama_kategori:'',
        }
      }
    },
    created () {
      this.getData()
    },

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Tambah Kategori' : 'Edit Kategori'
      }
    },

    methods: {
      getData () {
        this.$axios.$get('kategori')
        .then(response => {
            this.List = response.data
            console.log('LIST Data Kategori',this.List)
          })
        .catch(error => console.log(error))
      },

      editItem (item) {
        this.editedIndex = this.List.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.editedItem.id_kategori = item.id_kategori
        // console.log('idnya',item.id_kategori)
        // this.editedItem.nama_kategori = this.List.find(e => e.id_kategori === this.editedItem.id_kategori)                  
        this.dialog = true
      },

      deleteItem (item) {
        const index = this.List.indexOf(item)
        this.editedItem = Object.assign({}, item)
        
        Swal.fire({
          title: 'Hapus Kategori',
          text: "Apakah Anda yakin Menghapus Kategori !",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Ok',
          cancelButtonText: 'Batal',
          showLoaderOnConfirm: true,
          showCancelButton: true,
        })
        .then((result) => {
          if (result.value) {
            this.$axios.$delete("kategori/"+this.editedItem.id_kategori).then(response => {
            this.swalMessage('Data Berhasil Dihapus')
            this.getData();
          })
          console.log('idnya', editedItem.id_kategori )
          }
        }) 
        // 
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
      // save () {
        // this.$axios.$post("kategori", {
        //   nama_kategori: this.nama_kategori
        // })
        // .then(response => {
        //   console.log(response)
        //   this.getData();
        //   this.dialog = false;
        // })
        // .catch(error => {
        //   console.log(error)
        //   this.getData();
        // });
      // },

      save () {
         this.editedItem.id_kategori = this.editedItem.id_kategori
        if (this.editedIndex > -1) {
          let tableItem = this.editedItem
          Object.assign(this.List[this.editedIndex], this.editedItem)
          return this.$axios
          .$put("kategori/" + this.editedItem.id_kategori,tableItem)
          
          .then(response => {
            this.x = response;
            this.getData();
            this.dialog = false;
          })
          console.log('item',this.editedItem.id_kategori)
          .catch(error => {
            console.log(error)
            this.getData()
          })
        } else {
          let tableItem = this.editedItem
          this.List.push(this.editedItem)
          this.$axios.$post("kategori", tableItem)
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
    }
  }
</script>