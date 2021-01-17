<template>
  <div class="container column">
    <app-form @saveBlock="saveBlock"></app-form>

    <div class="card card-w70">
      <h3 v-if="isResumeEmpty">Добавьте первый блок, чтобы увидеть результат</h3>

      <div v-else>
        <component 
          v-for="block in resume"
          :is="'app-' + block.type"
          :key="block.id"
          :body="block.body"
          v-bind="{ body: block.body }"
        >  </component>
        <app-loader v-if="loadingBD"></app-loader>
      </div>
    </div>
  </div>

  <div class="container">
    <p>
      <button class="btn primary" @click="loadComments">
        Загрузить комментарии
      </button>
    </p>

    <app-loader v-if="loading"></app-loader>
    <app-comments v-else :comments="comments"></app-comments>
  </div>
</template>

<script>
import AppLoader from "./components/AppLoader"
import AppComments from "./components/AppComments"
import AppForm from "./components/AppForm"
import AppAvatar from "./components/AppAvatar"
import AppText from "./components/AppText"
import AppTitle from "./components/AppTitle"
import AppSubtitle from "./components/AppSubtitle"

export default {
  data() {
    return {
      loading: false,
      loadingBD: false,
      comments: [],
      resume: []
    }
  },
  mounted() {
    this.loadResume()
  },
  methods: {
    async saveBlock(newBlock) {
      try {
        this.loadingBD = true
        const response = await fetch(
          "https://vue-resume-week2-default-rtdb.firebaseio.com/resume.json",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(newBlock),
          }
        )

        const firebazeData = await response.json()
        this.resume.push({
          id: firebazeData.name,
          type: newBlock.type,
          body: newBlock.body
        })
        this.loadingBD = false
      } catch (error) {
        console.log(`Ошибка сохранения: ${error}`)
      }
    },
    async loadResume() {
      try {
        const response = await fetch("https://vue-resume-week2-default-rtdb.firebaseio.com/resume.json");
        const data = await response.json();
        if(Object.keys(data).length != 0){
          this.resume = Object.keys(data).map( key => {
            return {
              id: key,
              type: data[key].type,
              body: data[key].body
            }
          })
        }
      } catch (error) {
        console.log(`Ошибка базы данных: ${error}`)
      }
    },
    async loadComments() {
      try {
        this.loading = true
        const response = await fetch("https://jsonplaceholder.typicode.com/comments?_limit=42");
        this.comments = await response.json();
        this.loading = false
      } catch (error) {
        console.log(`Неудалось загрузить комментарии. Ошибка базы данных: ${error}`)
      }
    }
  },
  computed: {
    isResumeEmpty(){
      return this.resume.length === 0
    }
  },
  components: {
    AppLoader,
    AppComments,
    AppForm,
    AppAvatar,
    AppText,
    AppTitle,
    AppSubtitle
  }

}

</script>

<style>
.avatar {
  display: flex;
  justify-content: center;
}

.avatar img {
  width: 150px;
  height: auto;
  border-radius: 50%;
}
</style>
