<template>
  <v-layout>
    <v-flex>
      <v-data-table
        dense
        :headers="headers"
        :items="agencias"
        :search="search"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Agencias</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-text-field
              class="text-xs-center"
              v-model="search"
              append-icon="search"
              label="Busqueda"
              single-line
              hide-details
            ></v-text-field>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  small
                  v-bind="attrs"
                  v-on="on"
                >
                  Nueva Agencia
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="12" md="12">
                        <v-text-field
                          v-model="name"
                          label="Agencia"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="12">
                        <v-text-field
                          v-model="address"
                          label="Dirección"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="12">
                        <v-text-field
                          v-model="legal_representative"
                          label="Representante Legal"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          v-model="rfc"
                          label="RFC"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field v-model="email" label="Email"></v-text-field>
                      </v-col>
                 
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close">
                    Cancelar
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="guardar">
                    Guardar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-dialog v-model="adModal" max-width="290">
                <v-card-title  v-if="adAccion==1">
                    Activar Agencia
                </v-card-title >
                   <v-card-title  v-if="adAccion==2">
                     Desactivar Agencia
                </v-card-title >

                <v-card-text>
                    Estas a punto de <span v-if="adAccion==1">activar</span><span v-if="adAccion==2">desactivar</span>
                    la agencia {{adNombre}}
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn  @click="activarDesactivarCerrar()" small text color="orange">
                        Cancelar
                    </v-btn>
                     <v-btn v-if="adAccion==1" @click="activar()" small text color="primary">
                    Activar
                    </v-btn>
                     <v-btn v-if="adAccion==2" @click="deactivate()" small text color="primary">
                    Desactivar
                    </v-btn>
                </v-card-actions>
            </v-dialog>
          
          </v-toolbar>
        </template>
        <template v-slot:item.opciones="{ item }">
        <template>
             <v-icon small class="mr-2" @click="editItem(item)"> edit </v-icon>
        </template>
         <template v-if="item.status==0">
            <v-icon small @click="activarDesactivarMostrar(2,item)"> block </v-icon>
         </template>
               <template v-else>
            <v-icon small @click="activarDesactivarMostrar(1,item)"> check </v-icon>
         </template>
          
        </template>
       
        

        <template v-slot:item.status="{ item }">
          <v-chip
            class="ma-1"
            color="green"
            text-color="white"
            small
            v-if="item.status == 0"
          >
            <v-avatar left>
              <v-icon>verified</v-icon>
            </v-avatar>
            Activo
          </v-chip>
          <v-chip
            class="ma-2"
            color="red"
            text-color="white"
            small
            v-if="item.status == 1"
          >
            <v-avatar left>
              <v-icon>error_outline</v-icon>
            </v-avatar>
            Inactivo
          </v-chip>
        </template>

        <template v-slot:no-data>
          <v-btn color="primary" @click="listar()"> Reset </v-btn>
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      dialog: false,
      dialogDelete: false,
      legal_representative: "",
      _id: "",
      name: "",
      address:'',
      rfc: "",
      email: "",
      search:'',
      adModal:0,
      adAccion:0,
      adNombre:'',
      adId:'',
    
      agencias: [],
      headers: [
        {
          text: "Opciones",
          value: "opciones",
          sortable: false,
          sortable: false,
        },
        { text: "Agencia", value: "name", sortable: false, sortable: false },
        {
          text: "Direccion",
          value: "address",
          sortable: false,
          sortable: false,
        },
        { text: "Representante Legal", value: "legal_representative", sortable: false, sortable: false },
        { text: "RFC", value: "rfc", sortable: false, sortable: false },
        { text: "Email", value: "email", sortable: false, sortable: false },
             { text: "Estatus", value: "status", sortable: false, sortable: false },

      ],

      editedIndex: -1,
      editedItem: {},
      defaultItem: {},
    };
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo" : "Editar";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.listar();
  },

  methods: {
    listar() {
      let me = this;
      axios
        .get("agency/list")
        .then(function (response) {
          me.agencias = response.data;
        })
        .catch(function (error) {
          console.log(errror);
        });
    },
    limpiar() {
      (this._id = ""),
        (this.name = ""),
        (this.address = ""),
        (this.legal_representative = ""),
        (this.rfc = ""),
        (this.email = "");
        this.editedIndex = -1
    },

    guardar() {
      let me = this;
      if (this.editedIndex > -1) {
        axios
          .put("agency/update", {
            '_id':this._id,
            name: this.name,
            address: this.address,
            legal_representative: this.legal_representative,
            rfc: this.rfc,
            email: this.email
          
          })
          .then(function (response) {
            me.limpiar();
            me.close();
            me.listar();
          })
          .catch(function (error) {
            console.log(error);
          });
      } else {
        //guardar
        axios
          .post("agency/add", {
          name: this.name,
            address: this.address,
            legal_representative: this.legal_representative,
            rfc: this.rfc,
            email: this.email,
          })
          .then(function (response) {
            me.limpiar();
            me.close();
            me.listar();
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },

  
    editItem(item) {
      this.name = item.name;
      this._id = item._id;
      this.address = item.address;
      this.legal_representative = item.legal_representative;
      this.rfc = item.rfc;
      this.email = item.email;
      
      this.dialog = true;
      this.editedIndex = 1;
    },

  activarDesactivarMostrar(accion,item){
      this.adModal = 1;
      this.adNombre = item.name;
      this.adId = item._id
        if(accion ==1){ 
            this.adAccion= 1
        }else if(accion==2){
            this.adAccion = 2
        } else{
            this.adModal=0;
        }
  },

  activarDesactivarCerrar(){
      this.adModal = 0;
  },

   activar() {
      let me = this;
 
      axios
        .put("agency/activate", { _id: this.adId })
        .then(function(response) {
          (me.adModal = 0),
            (me.adAccion = 0),
            (me.adNombre = ""),
            (me.adId = ""),
            me.listar();
        })
        .catch(function(error) {
          console.log(error);
        });
    },
     deactivate() {
      let me = this;
      
      axios
        .put("agency/deactivate", { _id: this.adId })
        .then(function(response) {
          (me.adModal = 0),
            (me.adAccion = 0),
            (me.adNombre = ""),
            (me.adId = ""),
            me.listar();
        })
        .catch(function(error) {
          console.log(error);
        });
    },


    deleteItemConfirm() {
      this.desserts.splice(this.editedIndex, 1);
      this.closeDelete();
    },

   
    close() {
      this.dialog = false;
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem);
      } else {
        this.desserts.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>
