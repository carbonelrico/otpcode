<template>
  <div class="page">
    <h1>SMS OTP Demo</h1>
    <div class="panel">
      <p>
        Enter the code below or use the mock SMS button to fill it automatically.
        On iPhone Safari, the code input uses <strong>autocomplete="one-time-code"</strong>.
      </p>

      <div class="input-group">
        <label for="otp">One-time code</label>
        <input
          id="otp"
          v-model="otp"
          type="text"
          inputmode="numeric"
          autocomplete="one-time-code"
          placeholder="123456"
          maxlength="6"
        />
      </div>

      <div class="buttons">
        <button @click="generateMockCode" :disabled="smsPending">Mock SMS code</button>
        <button @click="verify" :disabled="!otp.length">Verify</button>
      </div>

      <p class="hint" v-if="smsPending">
        Waiting for mock SMS…
      </p>

      <p v-if="status" :class="statusClass">{{ status }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const otp = ref('')
const mockCode = ref('')
const status = ref('')
const smsPending = ref(false)

const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms))

const generateMockCode = async () => {
  otp.value = ''
  mockCode.value = ''
  status.value = 'Waiting for mock SMS…'
  smsPending.value = true

  const code = Math.floor(100000 + Math.random() * 900000).toString()
  await sleep(1800)

  mockCode.value = code
  otp.value = code
  smsPending.value = false
  status.value = 'Mock SMS arrived and filled automatically.'
}

const verify = () => {
  if (!otp.value) {
    status.value = 'Please enter the code.'
    return
  }

  if (otp.value === mockCode.value) {
    status.value = '✅ Code verified successfully!'
  } else {
    status.value = '❌ Code does not match the mock SMS code.'
  }
}

const statusClass = computed(() => {
  if (!status.value) return ''
  return status.value.includes('successfully') ? 'success' : 'error'
})
</script>

<style>
body {
  margin: 0;
  font-family: system-ui, sans-serif;
  background: #f4f6fb;
  color: #172c4a;
}

.page {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 24px;
}

.panel {
  width: min(560px, 100%);
  padding: 28px;
  border-radius: 18px;
  background: white;
  box-shadow: 0 24px 60px rgba(29, 33, 58, 0.12);
}

h1 {
  margin-top: 0;
}

.input-group {
  margin: 20px 0;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
}

input {
  width: 100%;
  border: 1px solid #d7e0f0;
  border-radius: 14px;
  padding: 14px 16px;
  font-size: 18px;
}

.buttons {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

button {
  border: none;
  border-radius: 12px;
  padding: 12px 18px;
  cursor: pointer;
  font-weight: 600;
  background: #3b82f6;
  color: white;
}

button:disabled {
  opacity: 0.55;
  cursor: not-allowed;
}


.hint {
  margin-top: 16px;
  color: #475569;
}

.success {
  color: #047857;
  margin-top: 16px;
}

.error {
  color: #b91c1c;
  margin-top: 16px;
}
</style>
