<template>
  <form class="card card-w30">
    <div class="form-control">
      <label for="type">Тип блока</label>
      <select id="type" v-model="blockType">
        <option
          v-for="(val, key) in types"
          :value="key"
          :key="key"
        >{{ val }}</option>
      </select>
    </div>

    <div class="form-control">
      <label for="value">Значение</label>
      <textarea id="value" rows="3" v-model.trim="blockText"></textarea>
    </div>

    <button
      class="btn primary"
      :disabled="!readyToSave"
      @click.prevent="saveBlock"
    >Добавить</button>
  </form>
</template>

<script>
export default {
  emits: {
    saveBlock(newBlock){
      if (Object.keys(newBlock).length !== 0){
        return true
      }
      console.log("Неверные данные в форме")
      return false
    } 
  },
  data() {
    return {
      types: {
        title: "Заголовок",
        subtitle: "Подзаголовок",
        avatar: "Аватар",
        text: "Текст"
      },
      blockText: "",
      blockType: "title"
    }
  },
  methods: {
    saveBlock() {
      const newBlock = {
        type: this.blockType,
        body: this.blockText
      }
      this.$emit("saveBlock", newBlock)
      this.blockText = ""
      this.blockType = "title"
    }
  },
  computed: {
    readyToSave() {
      return this.blockText.length >= 3
    }
  }
}
</script>
