<template>
  <input
    class="form-control"
    :placeholder="title + ' filter'"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)"
  />
</template>

<script>
export default {
  props: {
    title: String,
    value: String
  }
}
</script>

<style scoped>
</style>
