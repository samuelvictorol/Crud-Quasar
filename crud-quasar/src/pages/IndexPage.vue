<template>
  <q-page padding>
    <div class="q-pa-md">
      <q-table title="Posts" :rows="posts" :columns="columns" row-key="name">
        <template v-slot:top>
          <q-btn color="primary" :disable="loading" label="Add row" @click="{ }" />
          <q-space />
          <q-input borderless dense debounce="300" color="primary" v-model="filter">
            <template v-slot:append>
              <q-icon name="search" />
            </template>
          </q-input>
        </template>
        <template v-slot:body-cell-actions="{ props }">
          <q-td :props="props" class="q-table-col-auto-width">
            <q-btn icon="remove" dense size="sm" @click="handleDeletePost(props.row.id)" class="q-ml-xs" />
          </q-td>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const columns = [
      { name: 'id', field: 'id', label: 'id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'title', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'author', sortable: true, align: 'left' },
      { name: 'content', field: 'content', label: 'content', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'actions', sortable: true, align: 'right' }
    ]
    const $q = useQuasar()
    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        console.log(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Confirm',
          message: 'Are you sure you want to delete this post?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({
            color: 'positive',
            message: 'Post deleted successfully',
            position: 'top'
          })
          getPosts()
        })
      } catch (error) {
        alert(error.message)
      }
    }

    return {
      posts,
      columns,
      handleDeletePost
    }
  }
})
</script>
<style>
.q-table-col-auto-width {
  width: 1%;
  white-space: nowrap;
}
</style>
