<template>
  <div class="avatar">
    <img :src="body">
  </div>
</template>

<script>
export default {
  props: {
    body: String
  },
}
</script>
