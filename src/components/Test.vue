<template>
  <div id="app">
  <v-app id="inspire">
    <v-app id="inspire">
      <v-navigation-drawer
        v-model="drawer"
        app
        clipped
      >
        <v-list dense>
            <v-subheader class="grey--text text--darken-1">Desarrollo de Negocios</v-subheader>
          <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>add_task</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
               Confirmar Servicios
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
                <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>timeline</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
               Movimientos
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
               <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>source</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
               Servicios Archivados
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-subheader class="grey--text text--darken-1">Logistica y Agenciamiento</v-subheader>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>toggle_on</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
              On-Hire
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
           <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>toggle_off</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
              Off-Hire
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>timeline</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
               Movimientos
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>

             <v-subheader class="grey--text text--darken-1">Radio Operador</v-subheader>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>add_task</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
              Servicios Confirmados
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
       
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>timeline</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
               Movimientos
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>

             <v-subheader class="grey--text text--darken-1">Facturación y Mesa de Control</v-subheader>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>content_paste</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
             Facturas y Estimación
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>

          
             <v-subheader class="grey--text text--darken-1">Agencia</v-subheader>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>anchor</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
             Mis Servicios
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
               <v-subheader class="grey--text text--darken-1">Administrador</v-subheader>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>business</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
            Agencias
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
               <v-list-item :to="{name:'embarcaciones'}">
            <v-list-item-action>
              <v-icon>directions_boat</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
            Embarcaciones
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
            <v-list-item
           
            link
          >
            <v-list-item-action>
              <v-icon>how_to_reg</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>
            Usuarios
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
       
           
        
        </v-list>
      </v-navigation-drawer>
     
      <v-app-bar
        app
        clipped-left
        color="blue"
        dense
      >
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-icon
          class="mx-4"
          
        >
         desktop_mac
        </v-icon>
        <v-toolbar-title class="mr-12 align-center">
          <span class="title">Seguimiento de Servicios</span>
        </v-toolbar-title>
        <v-spacer></v-spacer>
      
      </v-app-bar>
  
   
    </v-app>
  </v-app>
</div>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  components: { HelloWorld },
  name: 'App',
  componentes:{
    HelloWorld
  },
  data() {
    return{
          drawer: null,
   
    }
    
  },
  created () {
    this.$vuetify.theme.dark = true
  },

};
</script>
