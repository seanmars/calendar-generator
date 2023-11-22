<script setup lang="ts">
import { ref } from 'vue';
import {
  NSpace,
  NLayout,
  NButton,
  NInput,
  NForm,
  FormInst,
  useMessage
} from 'naive-ui';

interface InterfaceInput {
  user: {
    name: string;
    age: string;
  };
  phone: string;
}

const formRef = ref<FormInst | null>(null);
const message = useMessage();
const formValue = ref({
  user: {
    name: '',
    age: ''
  },
  phone: ''
});

const rules = {
  user: {
    name: {
      required: true,
      message: 'Please input your name',
      trigger: 'blur'
    },
    age: {
      required: true,
      message: 'Please input your age',
      trigger: ['input', 'blur']
    }
  },
  phone: {
    required: true,
    message: 'Please input your number',
    trigger: ['input']
  }
};

const handleSubmit = () => {
  message.info('Button Clicked');
};
</script>

<template>
  <n-form
      ref="formRef"
      inline
      :label-width="80"
      :model="formValue"
      :rules="rules"
      size="large"
  >
    <n-layout content-style="padding: 24px;">
      <n-layout :content-style="{'display': 'flex', 'align-items': 'center', 'justify-content': 'center'}">
        <n-space vertical size="small" class="mainForm">
          <n-form-item label="Name" path="user.name">
            <n-input v-model:value="formValue.user.name" placeholder="Input Name" />
          </n-form-item>
          <n-form-item label="Age" path="user.age">
            <n-input v-model:value="formValue.user.age" placeholder="Input Age" />
          </n-form-item>
          <n-form-item label="Phone" path="phone">
            <n-input v-model:value="formValue.phone" placeholder="Phone Number" />
          </n-form-item>
          <n-form-item>
            <n-button type="primary" @click="handleSubmit">
              Validate
            </n-button>
          </n-form-item>
        </n-space>
      </n-layout>
    </n-layout>
  </n-form>
</template>

<style scoped>
.mainForm {
  width: 100%;
  max-width: 400px;
}
</style>