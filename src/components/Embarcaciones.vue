<template>
  <v-layout>
    <v-flex>
      <v-data-table
        :headers="headers"
        :items="embarcaciones"
        :search="search"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Embarcaciones</v-toolbar-title>
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
                  Nueva Embarcación
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="name"
                          label="Nombre"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="shipowner"
                          label="Armador"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="type"
                          label="Tipo"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="flag"
                          label="Bandera"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="imo" label="IMO"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="draft"
                          label="Calado"
                        ></v-text-field>
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
                    Activar Embarcación
                </v-card-title >
                   <v-card-title  v-if="adAccion==2">
                     Desactivar Embarcación
                </v-card-title >

                <v-card-text>
                    Estas a punto de <span v-if="adAccion==1">activar</span><span v-if="adAccion==2">desactivar</span>
                    la embarcacion {{adNombre}}
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
          <v-btn color="primary" @click="initialize"> Reset </v-btn>
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
      search: "",
      _id: "",
      name: "",
      shipowner: "",
      draft: "",
      adModal:0,
      adAccion:0,
      adNombre:'',
      adId:'',
      type: "",
      flag: "",
      imo: "",
      embarcaciones: [],
      headers: [
        {
          text: "Opciones",
          value: "opciones",
          sortable: false,
          sortable: false,
        },
        { text: "Nombre", value: "name", sortable: false, sortable: false },
        {
          text: "Armador",
          value: "shipowner",
          sortable: false,
          sortable: false,
        },
        { text: "Tipo", value: "type", sortable: false, sortable: false },
        { text: "Bandera", value: "flag", sortable: false, sortable: false },
        { text: "IMO", value: "imo", sortable: false, sortable: false },
        { text: "Calado", value: "draft", sortable: false, sortable: false },
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
        .get("boat/list")
        .then(function (response) {
          me.embarcaciones = response.data;
        })
        .catch(function (error) {
          console.log(errror);
        });
    },
    limpiar() {
      (this._id = ""),
        (this.shipowner = ""),
        (this.draft = ""),
        (this.type = ""),
        (this.flag = ""),
        (this.imo = "");
        this.editedIndex = -1
    },

    guardar() {
      let me = this;
      if (this.editedIndex > -1) {
        axios
          .put("boat/update", {
            '_id':this._id,
            name: this.name,
            shipowner: this.shipowner,
            draft: this.draft,
            type: this.type,
            flag: this.flag,
            imo: this.imo,
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
          .post("boat/add", {
            name: this.name,
            shipowner: this.shipowner,
            draft: this.draft,
            type: this.type,
            flag: this.flag,
            imo: this.imo,
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

    initialize() {
      this.desserts = [
        {
          name: "Frozen Yogurt",
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
        },
      ];
    },

    editItem(item) {
      this.name = item.name;
      this._id = item._id;
      this.shipowner = item.shipowner;
      this.draft = item.draft;
      this.type = item.type;
      this.flag = item.flag;
      this.imo = item.imo;
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
        .put("boat/activate", { _id: this.adId })
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
        .put("boat/deactivate", { _id: this.adId })
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

    activar(){
        let me = this;

          axios
        .put("boat/activate", { _id: this.adId })
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
