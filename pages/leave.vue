<template>
  <div class="my-content">
    <div class="container pt-6 pb-6">
      <v-card>
        <v-card-title class="text-h5 justify-center pt-10"
          >退会する</v-card-title
        >
        <v-card-text class="text-center"
          >一度退会すると投稿データは削除され、復元することができません。</v-card-text
        >
        <v-card-actions class="justify-center pt-5 pb-10">
          <v-btn color="#ff4081" outlined @click="leave()"
            >同意して退会する</v-btn
          >
        </v-card-actions>
      </v-card>
      <v-snackbar v-model="snackbar" :timeoute="5000" color="#ff4081" outlined>
        アカウントを削除しました。反映には時間がかかります。
      </v-snackbar>
    </div>
  </div>
</template>

<script>
import firebase from '~/plugins/firebase'
import headMeta from '~/mixins/headMeta.js'

export default {
  middleware: 'authenticated',
  mixins: [headMeta],
  data() {
    return {
      meta: {
        title: '退会 | from 8号車',
        description: '退会ページです。',
      },
      snackbar: false,
    }
  },
  computed: {
    user() {
      return this.$store.getters['auth/user']
    },
  },
  methods: {
    async leave() {
      const functions = firebase.app().functions('asia-northeast1')
      const deleteUser = functions.httpsCallable('deleteUser')

      const res = await deleteUser(this.user.uid).then(() => {
        this.snackbar = true
        firebase
          .auth()
          .signOut()
          .then(() => {
            this.$router.push({ path: '/' })
            this.$store.dispatch('auth/logout')
          })
      })
      return res
    },
  },
}
</script>
