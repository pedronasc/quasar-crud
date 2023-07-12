<template>
  <q-page padding>
    <q-form
      @submit="onSubmit"
      class="row q-col-gutter-sm"
    >
   <q-input
        outlined
        v-model="form.title"
        label="Titulo"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo Obrigatorio']"
    />
    <q-input
        outlined
        v-model="form.author"
        label="Autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo Obrigatorio']"
    />

    <div class="col-lg-12 col-xs-12">
      <q-editor
        v-model="form.content"
        min-height="5rem"
      />
    </div>

    <div class="col-12 q-gutter-sm">
      <q-btn
        label="Salvar"
        icon="save"
        color="primary"
        class="float-right"
        type="submit">
      </q-btn>
      <q-btn
        label="Cancelar"
        icon="save"
        color="white"
        class="float-right"
        text-color="primary"
        :to="{ name: 'home'}">
      </q-btn>
    </div>

    </q-form>

  </q-page>
</template>

<script>

import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const { post, getById, update } = postsService()
    const form = ref({
      title: '',
      author: '',
      content: ''
    })

    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()

    onMounted(async () => {
      if (route.params.id) {
        getposts(route.params.id)
      }
    })

    const getposts = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
        console.log(response)
      } catch (error) {
        console.error(error)
      }
    }

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value)
        } else {
          await post(form.value)
        }

        $q.notify({ message: 'Salvo com sucesso', icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }

})
</script>
