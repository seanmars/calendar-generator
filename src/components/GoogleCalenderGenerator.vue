<script setup lang="ts">
import { reactive, ref } from 'vue';
import dayjs from 'dayjs';
import 'dayjs/locale/zh-tw.js';
import { google } from 'calendar-link';
import {
  FormInst,
  NButton,
  NCheckbox,
  NDatePicker,
  NForm,
  NFormItem,
  NInput,
  NLayout,
  NSelect,
  NSpace,
  useMessage
} from 'naive-ui';

/*
1.姓名
2.時間(ex: 2023/11/22 0930-18:00，盡量不要請了又再更動)
3.假別(特休、病假、事假...等等)
4.事由(若不方便打出，請私敲主管)
5.代理人(姓名，若不知道請詢問主管)
* */
interface InterfaceInput {
  name: string;
  leaveType: string;
  reason: string;
  agentName: string;
}

const Hour24: number = 86400000;
const Hour8: number = 28800000;

dayjs.locale('zh-tw');

const message = useMessage();
const timePickerProps = {
  format: 'HH:mm',
  timeZone: 'Asia/Taipei',
  hours: [9, 12, 18],
  minutes: [0, 30],
};

const formRef = ref<FormInst | null>(null);
const formValue = reactive<InterfaceInput>({
  name: '',
  leaveType: '',
  reason: '',
  agentName: ''
});

const leaveTypeOptions = [
  {
    label: '特休',
    value: '特休',
    disabled: false
  },
  {
    label: '病假',
    value: '病假',
    disabled: false
  },
  {
    label: '事假',
    value: '事假',
    disabled: false
  },
  {
    label: '其他',
    value: '其他',
    disabled: false
  },
];
const rules = {};

const isAllDay = ref<boolean>(false);
const timestamp = ref<[number, number] | null>(null);
const resultValue = ref<string>('');

const handleSubmit = () => {
  if (!formValue.name) {
    message.error('請輸入請假人姓名');
    return;
  }

  if (!timestamp.value || !timestamp.value[0] || !timestamp.value[1]) {
    message.error('請選擇請假時間');
    return;
  }

  if (!formValue.leaveType) {
    message.error('請選擇假別');
    return;
  }

  if (!formValue.agentName) {
    message.error('請輸入代理人');
    return;
  }

  const desc = `假別：${formValue.leaveType}\n事由：${formValue.reason}\n代理人：${formValue.agentName}`;

  // use days to get start time
  const startTime = dayjs(timestamp.value[0]);
  const event = {
    title: `${startTime.format('HH:mm')} ${formValue.name} ${formValue.leaveType}`,
    description: desc,
    start: new Date(timestamp.value[0]),
    end: new Date(timestamp.value[1]),
    busy: false
  };

  resultValue.value = google(event);
};

const handleMakeEvent = () => {
  // open the url in a new window
  if (!resultValue.value) {
    return;
  }

  window.open(resultValue.value);
};

const onIsAllDayChanged = (value: boolean): void => {
  if (!timestamp.value) {
    return;
  }

  if (value) {
    const start = (timestamp.value[0] - (timestamp.value[0] - Hour8) % Hour24) + Hour8;
    const end = (timestamp.value[1] - (timestamp.value[1] - Hour8) % Hour24) + Hour8;
    timestamp.value = [start, end];
  } else {
    const start = timestamp.value[0];
    const end = timestamp.value[1];
    timestamp.value = [start, end];
  }
};

</script>

<template>
  <div class="container">
    <n-space justify="center">
      <div>
        <!--  Form  -->
        <n-layout>
          <n-form
              ref="formRef"
              label-placement="left"
              :label-width="120"
              :model="formValue"
              :rules="rules"
              size="large"
          >
            <n-space vertical size="small" class="mainForm">
              <n-form-item label="請假人姓名" path="name">
                <n-input v-model:value="formValue.name" placeholder="姓名" />
              </n-form-item>

              <n-space justify="end">
                <n-checkbox
                    style="width: 100%;"
                    size="large"
                    label="全天"
                    v-model:checked="isAllDay"
                    @update="onIsAllDayChanged" />
              </n-space>

              <n-form-item label="請假時間" path="name">
                <n-date-picker
                    format="yyyy-MM-dd HH:mm"
                    :time-picker-props="timePickerProps"
                    :type="isAllDay?'daterange':'datetimerange'"
                    clearable
                    :actions="['clear', 'confirm']"
                    v-model:value="timestamp"
                />
              </n-form-item>

              <!--  假別  -->
              <n-form-item label="假別" path="reason">
                <n-select v-model:value="formValue.leaveType" :options="leaveTypeOptions" />
              </n-form-item>

              <!--  事由  -->
              <n-form-item label="事由" path="reason">
                <n-input v-model:value="formValue.reason" type="textarea" placeholder="事由" />
              </n-form-item>

              <!--  代理人  -->
              <n-form-item label="代理人" path="agentName">
                <n-input v-model:value="formValue.agentName" type="text" placeholder="代理人" />
              </n-form-item>

              <n-form-item>
                <n-space justify="end">
                  <n-button type="primary" @click="handleSubmit">
                    Generate Google Event
                  </n-button>
                </n-space>
              </n-form-item>
            </n-space>
          </n-form>
        </n-layout>

        <!--  Result  -->
        <n-layout>
          <n-input v-model:value="resultValue" type="textarea" placeholder="" readonly />
          <n-space justify="end">
            <n-button type="primary" @click="handleMakeEvent" size="large">
              Create Event
            </n-button>
          </n-space>
        </n-layout>
      </div>
    </n-space>
  </div>
</template>

<style scoped>
.container {
  padding: 24px;
  justify-content: center;
  align-items: center;
  display: flex;
}

.container div {
  width: 500px;
}
</style>