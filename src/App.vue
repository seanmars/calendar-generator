<script setup lang="ts">
import { NConfigProvider, NMessageProvider } from 'naive-ui';
import { zhTW, dateZhTW } from 'naive-ui';
import { darkTheme } from 'naive-ui';
import { NLayout } from 'naive-ui';

import GoogleCalenderGenerator from './components/GoogleCalenderGenerator.vue';
</script>

<template>
  <n-config-provider :locale="zhTW" :date-locale="dateZhTW" :theme="darkTheme" style="height: 100vh;">
    <n-global-style>
      <n-message-provider>
        <n-layout style="height: 100%">
          <n-layout-content>
            <google-calender-generator />
          </n-layout-content>
        </n-layout>
      </n-message-provider>
    </n-global-style>
  </n-config-provider>
</template>

<style scoped>

</style>
