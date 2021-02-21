<template>
  <div>
    <label for="send">
      <h2>{{ header }}</h2>
    </label>
    <span style="margin-right: 1.5em"> {{ currency }} </span>
    <!-- 5. Display the masked amount to the user. -->
    <input name="send" id="send" @input="onInput" :value="formatted" placeholder="93.50" />
    <!-- Order of events: Keydown => Keypress => Input => Keyup -->
    <!-- Keydown has the input value before it is propagated to input, 
    meaning it useful for validations -->
  </div>
</template>

<script lang="ts">
import { setup, ref, Ref, computed, toRefs } from 'vue';

/* The strategy is as follows:

1. Keep the raw version of the amount the user is inputting on the screen.
2. Create an input handler that removes the mask from the input updates the raw version on every user type.
3. Create a watcher/computed property that whenever the user types, adds a mask to the raw version.
4. When the raw version is updated, run the watcher/computed to update the masked amount.
5. Display the masked amount to the user.

*/
export default {
  props: {
    header: String,
    currency: String,
  },
  setup(props) {
    const { header, currency } = toRefs(props);
    // Keeps a raw version of the amount (i.e. no separators).
    // 1. Store the raw version of the amount the user is inputting on the screen.
    const rawAmount = ref<null | string>(null);

    // When user types, we update the raw amount,
    // triggering the computed property below.

    // 2. Create an input handler that removes the mask from the input updates the raw version on every user type.
    const onInput = (e) => {
      rawAmount.value = destroyMask(e.target.value);
    };

    // This shows the user the masked amount.
    // 3. Create a watcher/computed property that whenever the user types, adds a mask to the raw version.
    const formatted = computed(() => {
      // 4. When the raw version is updated, run the watcher/computed to update the masked amount.
      return useMask(rawAmount.value);
    });

    // Strips everything thats not a digit (i.e the separator) from the string.
    const destroyMask = (str: string) => {
      return str.replace(/\D+/g, '');
    };

    // Masks a string
    const useMask = (str: string) => {
      if (str?.length > 1) {
        // Has 2 digits at least, should build string.
        // Decimal: last 2 digits.
        let decimalsPart = str.substring(str.length - 2, str.length);
        // Integer: beginning until last 2 digits.
        let integerPart = str.substring(0, str.length - 2);

        // If first char is 0, skip it.
        // For example, user starts the input a 0.
        if (integerPart.substring(0, 1) === '0') {
          integerPart = integerPart.substring(1, integerPart.length);
        }
        // Concat the strings to form the masked number
        return `${integerPart}.${decimalsPart}`;
      } else if (str?.length === 1) {
        // Has only 1 digit => 0.0D
        return `0.0${str}`;
      } else {
        // Has no digit => 0.00
        return '0.00';
      }
    };
    return {
      rawAmount,
      onInput,
      formatted,
      header,
      currency,
    };
  },
};
</script>

<style scoped></style>
