<template>
    <div class="container">
        <v-card class="mx-auto" max-width="90%" color="white" outlined>
            <div class="text-field mx-auto" style="width:90%">
                <v-select
                    class="my-2"
                    :items="d_kategori"
                    item-text="nama_kategori"
                    item-value="id_kategori"
                    v-model="input_kategori"
                    label="Pilih Kategori"
                    @change="getData()"
                ></v-select>
            </div>
        </v-card>
        <div class="tabel mx-auto mt-6 pt-4" style="width:100%;">
            <v-row no-gutters mb="12">
                <v-col sm="1">
                    <v-card class="pa-2" outlined tile>
                        <th width="10%">No.</th>
                    </v-card>
                </v-col>
                <v-col sm="7">
                    <v-card class="pa-2" outlined tile>
                        <th width="30%">Nama Barang</th>
                    </v-card>
                </v-col>
                <v-col sm="4">
                    <v-card class="pa-2" outlined tile>
                        <th width="30%">Harga</th>
                    </v-card>
                </v-col>
            </v-row>
            <v-row v-for="(l, index) in List" :key="index" no-gutters mb="12">
                <v-col sm="1">
                    <v-card class="pa-2" outlined tile>
                        <td>{{index+1}}</td>
                    </v-card>
                </v-col>
                <v-col sm="7">
                    <v-card class="pa-2" outlined tile>
                        <td class="nama text-left">{{l.nama_barang}}</td>
                    </v-card>
                </v-col>
                <v-col sm="4">
                    <v-card class="pa-2" outlined tile>
                        <td class="nama text-left">Rp. {{l.harga}}</td>
                    </v-card>
                </v-col>
            </v-row>
        </div>
    </div>
</template>

<script>
    import axios from "axios"
    export default {
        data () {
            return {
                // headers: [
                // {
                //     text: 'Id Barang',
                //     align: 'start',
                //     sortable: false,
                //     value: 'id_kategori',
                //     sortable: true, class:'th-head'
                // },
                // { text: 'Nama Barang', value: 'nama_barang', sortable: true, class:'th-head'},
                // { text: 'Harga', value: 'harga', sortable: true, class:'th-head'},
                // ],
                List:[],
                d_kategori:[],
                input_kategori:"",
            }
        },
        created () {
            // this.getData()
            this.getDataKategori()
        },
        methods: {
            getData () {
                this.List = [];
                this.$axios.$get('search-barang/'+this.input_kategori)
                .then(response => {
                    this.List = response.data
                    console.log('responnya', response)
                })
                .catch(error => console.log(error))
            },

            getDataKategori (id_kategori) {
                this.$axios.$get('kategori')
                .then(response => {
                    this.d_kategori = response.data
                    console.log('Data',this.d_kategori)
                })
                .catch(error => console.log(error))
            },
        }
    }
</script>