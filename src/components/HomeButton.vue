<template>
  <button @click="goHome">Home</button>
</template>

<script>
export default {
  methods: {
    goHome() {
      this.$router.push({ path: '/' });
    }
  }
}
</script>
