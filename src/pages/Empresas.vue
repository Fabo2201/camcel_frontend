<!-- eslint-disable vue/valid-template-root -->
<template>
   <q-toolbar class="column">
      <q-input
        style="width: 100%;"
        filled
        class="custom-input"
        v-model="search"
        label="Busqueda"
      >
         <template v-slot:prepend>
            <q-btn flat round dense class="icono_de_busqueda" icon="search"/>
         </template>
      </q-input>

      <q-toolbar class="row reverse">
         <q-btn flat bordered @click="enterpriseCreate = true">
            <q-avatar icon="mdi-plus-circle-outline" />
         </q-btn>

         <create-empresa v-if="enterpriseCreate" :show="enterpriseCreate" @handleCloseCreateEnterprise="handleCloseCreateEnterprise" />
      </q-toolbar>
   </q-toolbar>

   <div v-if="!isLoading" class="q-pa-md row justify-center">
    <v-template  v-for="empresa in empresas" :key="empresa.id">
         <div v-if="search !== ''">
                 <card-empresas :empresa="empresa" v-if="empresa.nombre.toLowerCase().match(search.toLowerCase())"/>
              </div>
              <div v-else>
                <card-empresas :empresa="empresa"/>
              </div>
    </v-template>
   </div>
   <div v-if="isLoading" class="text-center">loading ...</div>
</template>

<script>
import CardEmpresas from 'src/components/CardEmpresas.vue';
import CreateEmpresa from "src/components/CreateEmpresa.vue"
import { useEnterpriseStore } from "src/store/enterprise.store"
import { api } from "src/boot/axios";
import { ref, computed } from "vue";

export default {
   components: {
      CardEmpresas,
      CreateEmpresa
   }, setup () {
      const enterpriseStore = useEnterpriseStore()
      const search = ref("");

      const enterpriseCreate = ref(false);
      const isLoading = ref(true)
      const empresas = computed(() => enterpriseStore.enterprises)

      const handleCloseCreateEnterprise = () => {
         enterpriseCreate.value = false
      }

      api.get("admin/enterprises").then((response) => {
         isLoading.value = false
         enterpriseStore.setEnterprises(response.data)
      })

      return {isLoading, empresas, search, enterpriseCreate, handleCloseCreateEnterprise}

   }
}


</script>
