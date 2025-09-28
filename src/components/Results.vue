<template>
  <div class="p-6 bg-gray-100 rounded shadow">
    <h2 class="text-xl font-bold mb-4">Risultati</h2>
    <div v-if="typeof punteggi.messaggio === 'string'">
      <p>{{ punteggi.messaggio }}</p>
    </div>
    <div v-else>
      <ul>
        <li v-for="(val, key) in punteggi" :key="key">
          {{ key }}: <strong>{{ val }}</strong>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Risultati',
  props: {
    punteggi: {
      type: Object,
      required: true,
    },
  },
}
</script>

<style scoped></style>
