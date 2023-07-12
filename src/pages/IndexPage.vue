<template>
  <q-page padding>
    <q-table
      title="Artigos"
      :rows="posts"
      :columns="columns"
      row-key="name"
    >
    <template v-slot:top>
      <span class="text-h5">Artigos</span>
      <q-space />
      <q-btn color="primary" label="Add row" :to="{ name: 'formpost'}" />
    </template>

    <template v-slot:body-cell-actions="props">
      <q-td :props="props" class="q-gutter-sm">
        <q-btn icon="edit" dense color="info" size="sm" @click="handleEditPost(props.row.id)"/>
        <q-btn icon="delete" dense color="negative" size="sm" @click="handleDeletePost(props.row.id)"/>
      </q-td>
    </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
// import { api } from 'boot/axios'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])

    const { list, remove } = postsService()

    const columns = [
      { name: 'id', field: 'id', label: 'Id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'Título', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'Autor', sortable: true, align: 'left' },
      { name: 'content', field: 'content', label: 'Conteudo', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'Ações', align: 'right' }
    ]

    const $q = useQuasar()
    const router = useRouter()

    onMounted(() => {
      getposts()
    })

    const getposts = async () => {
      try {
        const data = await list()
        // const { data } = await api.get('posts')
        // console.log(data)
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Confirmar',
          message: 'Deseja deletar esse post ?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
        // console.log('>>>> OK')
          await remove(id)
          // alert('Deletado com sucesso!!!')
          $q.notify({ message: 'Deletado com sucesso', icon: 'check', color: 'positive' })
          await getposts()
        })
      } catch (error) {
        $q.notify({ message: 'Error ao apagar post', icon: 'times', color: 'negative' })
        // alert(error)
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formpost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
