<template>
  <q-dialog v-model="show" >
    <q-card style="width: 900px;">
      <q-card-section>
        <div class="text-h6">Crear Empresa</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
         <q-form @submit.prevent="handleCreateEnterprise">
           <q-input name="RUT" label="RUT" v-model="dataCreateEnterprise.RUT"/>
           <q-input name="nombre" label="nombre" v-model="dataCreateEnterprise.nombre"/>
           <q-checkbox label="Verificado" v-model="dataCreateEnterprise.is_valid" />
           <q-file color="teal" filled label="image" v-model="dataCreateEnterprise.image">
             <template v-slot:prepend>
               <q-icon name="cloud_upload" />
             </template>
           </q-file>

           <p v-if="isLoadingUser">loading...</p>
           <q-select v-else v-model="dataCreateEnterprise.user_id" option-label="name" :options="user" label="Standard" />
           <q-btn label="Crear" class="q-mt-md" type="submit" color="primary"/>
         </q-form>
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="Cerrar" color="primary" v-close-popup @click="handleCloseCreateEnterprise" />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
import { reactive, toRef, ref } from "vue"
import { api } from "src/boot/axios";
import { useEnterpriseStore } from "src/store/enterprise.store";

export default {
  props: {
    show: {
      type: Boolean,
      required: true,
    }
  },
  setup(props, { emit }) {
    const enterpriseStore = useEnterpriseStore()

    const isLoadingUser = ref(true)
    const user = ref(null)

    api.get("admin/businessmen").then((response) => {
        isLoadingUser.value = false
        user.value = response.data
    })

    const show = toRef(props, "show")

    const dataCreateEnterprise = reactive({
      nombre: "", RUT: "", is_valid: false, image: null, user_id: null
    })

    const handleCloseCreateEnterprise = () => {
      emit("handleCloseCreateEnterprise")
    }

    const handleCreateEnterprise = () => {
        console.log(dataCreateEnterprise)
      api.post("admin/enterprises",
          {...dataCreateEnterprise, user_id: dataCreateEnterprise.user_id.id},
          {
            headers: dataCreateEnterprise.image ? { 'Content-Type': 'multipart/form-data' } : {'Content-Type': 'application/json'}
          }
      ).then((response) => {
        enterpriseStore.addEnterprise(response.data.enterprise)
        handleCloseCreateEnterprise()
      })
    }

    return {dataCreateEnterprise, handleCreateEnterprise, show, handleCloseCreateEnterprise, isLoadingUser, user}
  }
}
</script>
