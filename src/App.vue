<template>
  <money-transfer header="You're sending" currency="BRL" />
  <money-transfer header="They're receiving" currency="USD" />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import MoneyTransfer from './components/MoneyTransfer.vue';

export default defineComponent({
  name: 'App',
  components: {
    MoneyTransfer,
  },
});
</script>
