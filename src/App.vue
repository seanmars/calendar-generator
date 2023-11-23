<script setup lang="ts">
import { NConfigProvider, NMessageProvider } from 'naive-ui';
import { zhTW, dateZhTW } from 'naive-ui';

import GoogleCalenderGenerator from './components/GoogleCalenderGenerator.vue';
</script>

<template>
  <n-config-provider :locale="zhTW" :date-locale="dateZhTW">
    <n-message-provider>
      <google-calender-generator />
    </n-message-provider>
  </n-config-provider>
</template>

<style scoped>

</style>
