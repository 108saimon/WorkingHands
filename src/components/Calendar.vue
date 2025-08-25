<script setup>
import { defineOptions, defineProps } from 'vue';

defineOptions({
  name: 'Calendar'
});

defineProps({
  initialDate: {
    type: String,
    default: null
  },
  language: {
    type: String,
    default: 'ru'
  }
})


</script>