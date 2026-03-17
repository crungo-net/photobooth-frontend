<template>
  <q-card class="print-dialog" flat bordered>
    <q-form autofocus @submit="onSubmit" autocorrect="off" autocapitalize="off" autocomplete="off" spellcheck="false">
      <q-card-section class="dialog-caption">
        <div class="caption-text">{{ parameters_dialog_caption }}</div>
      </q-card-section>

      <q-card-section class="dialog-content">
        <div v-for="parameter in props.parameters" :key="parameter.key">
          <!-- ui_type=int -->
          <div v-if="parameter.ui_type == 'int'" class="int-control">
            <button
              type="button"
              class="stepper-btn"
              :disabled="parameter.valid_min && parseInt(formData[parameter.key]) <= parseInt(parameter.valid_min)"
              @click="formData[parameter.key] = String(parseInt(formData[parameter.key]) - 1)"
            >
              <span class="stepper-icon">&minus;</span>
            </button>

            <div class="number-display">
              <span>{{ formData[parameter.key] }}</span>
            </div>

            <button
              type="button"
              class="stepper-btn"
              :disabled="parameter.valid_max && parseInt(formData[parameter.key]) >= parseInt(parameter.valid_max)"
              @click="formData[parameter.key] = String(parseInt(formData[parameter.key]) + 1)"
            >
              <span class="stepper-icon">+</span>
            </button>

            <!-- hidden input for form validation -->
            <input type="hidden" :name="parameter.key" :value="formData[parameter.key]" />
          </div>

          <!-- ui_type=input (and else) -->
          <div v-else>
            <q-input
              filled
              v-model="formData[parameter.key]"
              :label="parameter.label"
              :key="parameter.key"
              :name="parameter.key"
              :rules="[
                (val) => (val && val.length > 0) || 'Please type something',
                (val) =>
                  (parameter.valid_max && val.length <= parseInt(parameter.valid_max)) || `Please type text longest ${parameter.valid_max} chars.`,
                (val) =>
                  (parameter.valid_min && val.length >= parseInt(parameter.valid_min)) || `Please type text shortest ${parameter.valid_min} chars.`,
              ]"
            />
          </div>
        </div>
      </q-card-section>

      <q-card-actions align="center" class="dialog-actions">
        <button type="submit" class="print-btn">
          {{ parameters_dialog_action_label }}
        </button>
      </q-card-actions>
    </q-form>
  </q-card>
</template>

<script setup lang="ts">
import { reactive } from 'vue'
import { onBeforeMount } from 'vue'
const formData = reactive({})

import type { components } from 'src/dto/api'

const props = defineProps<{
  config_index: number
  parameters_dialog_caption: string
  parameters_dialog_action_icon: string
  parameters_dialog_action_label: string
  parameters: components['schemas']['ShareProcessingParameters'][]
}>()

const emit = defineEmits<{
  triggerShareActionWithParameters: [config_index: number, input_data: object]
}>()

onBeforeMount(() => {
  props.parameters.forEach((parameter) => (formData[parameter.key] = parameter.default))
})

const onSubmit = () => {
  emit('triggerShareActionWithParameters', props.config_index, formData)
}
</script>

<style lang="scss" scoped>
.print-dialog {
  width: auto;
  min-width: 700px;
  background: linear-gradient(180deg, rgba(26, 26, 166, 0.2) 0%, rgba(255, 255, 255, 0.2) 100%), #ffffff;
  border: 10px solid #000000 !important;
  border-radius: 62px !important;
  box-shadow: 0px 0px 0px 5px rgba(255, 255, 255, 0.5), 10px 20px 0px #000000;
  padding: 40px 60px;
}

.caption-text {
  font-family: 'Kumbh Sans', 'Inter', sans-serif;
  font-weight: 500;
  font-size: 48px;
  line-height: 60px;
  text-align: center;
  color: #000000;
}

.dialog-content {
  display: flex;
  justify-content: center;
}

.int-control {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
}

.stepper-btn {
  width: 125px;
  height: 125px;
  background: linear-gradient(180deg, rgba(136, 136, 255, 0.5) 0%, rgba(197, 197, 255, 0) 100%);
  border: 8px solid #000000;
  border-radius: 30px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: opacity 0.2s;

  &:disabled {
    opacity: 0.3;
    cursor: not-allowed;
  }
}

.stepper-icon {
  font-size: 48px;
  font-weight: 700;
  color: #000000;
  line-height: 1;
}

.number-display {
  width: 170px;
  height: 170px;
  background: #ffffff;
  border-radius: 25px;
  display: flex;
  align-items: center;
  justify-content: center;

  span {
    font-family: 'Kumbh Sans', 'Inter', sans-serif;
    font-weight: 400;
    font-size: 96px;
    line-height: 1;
    color: #000000;
  }
}

.dialog-actions {
  padding-bottom: 20px;
}

.print-btn {
  width: 484px;
  height: 129px;
  background: linear-gradient(180deg, #ff8f00 0%, #ffdb80 100%);
  border: 8px solid #000000;
  border-radius: 50px;
  box-shadow: 0px 0px 2px 8px rgba(255, 255, 255, 0.5);
  cursor: pointer;
  font-family: 'Kumbh Sans', 'Inter', sans-serif;
  font-weight: 700;
  font-size: 48px;
  line-height: 60px;
  text-align: center;
  letter-spacing: 0.05em;
  color: #000000;
  transition: filter 0.2s;

  &:hover {
    filter: brightness(1.05);
  }

  &:active {
    filter: brightness(0.95);
  }
}
</style>
