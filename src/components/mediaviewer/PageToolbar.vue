<template>
  <q-page-sticky position="bottom" id="gallery-toolbar" class="toolbar" v-if="item">
    <div class="q-mb-lg action-buttons col">
      <div class="q-pa-md row flex flex-center">
        <q-btn v-if="showFilter && enableFilter" stack no-caps rounded color="primary"
          class="q-mr-sm action-button action-button-filter col-auto glass-effect" icon="sym_o_filter"
          :label="$t('BTN_LABEL_GALLERY_FILTER')" @click="invokeToggleDisplayFilter" />
        <q-btn v-if="showDownload" stack no-caps rounded color="primary"
          class="q-mr-sm action-button action-button-download col-auto glass-effect" icon="sym_o_download"
          :label="$t('BTN_LABEL_GALLERY_DOWNLOAD')" @click="openURL(`/sharepage/#?url=/media/full/${item.id}`)" />
        <ShareTriggerButtons v-if="showShare" :triggers="shareButtons"
          :current-item-is-image="isPrintableImage(item.unprocessed)" @trigger-action="invokeShareAction">
        </ShareTriggerButtons>
        <q-btn v-if="showDelete" stack flat no-ripple rounded
          class="q-mr-sm action-button action-button-delete col-auto" no-caps icon="img:/api/icons/delete.png"
          @click="confirmDeleteDialog = true" />
      </div>

      <div class="q-mr-sm row flex flex-center">
        <q-badge color="grey-8" class="q-mr-xs"> <q-icon name="sym_o_image" color="white" class="q-mr-xs" /> {{ item.id
          }}</q-badge>
        <q-badge color="grey-8" class="q-mr-xs" v-if="props.image_number && props.images_total">
          <q-icon name="sym_o_tag" color="white" class="q-mr-xs" />
          {{
            $t('LABEL_ELEMENT_X_OF_Y', {
              no: props.image_number,
              total: props.images_total,
            })
          }}
        </q-badge>
      </div>
    </div>
  </q-page-sticky>
  <q-dialog v-model="confirmDeleteDialog">
    <div id="gallery-confirm-delete-dialog" class="delete-dialog no-shadow">
      <div class="delete-caption">{{ $t('MSG_CONFIRM_DELETE_IMAGE') }}</div>
      <div class="delete-actions">
        <button class="delete-btn-no" @click="confirmDeleteDialog = false">No</button>
        <button class="delete-btn-yes"
          @click="[invokeDeleteMediaitem(item.id), confirmDeleteDialog = false, $emit('closeEvent')]">Yes</button>
      </div>
    </div>
  </q-dialog>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { openURL } from 'quasar'
import type { components } from 'src/dto/api'
import { isPrintableImage } from 'src/util/media_is_type'
import { default as ShareTriggerButtons, type ShareSchema } from '../ShareTriggerButtons.vue'
const confirmDeleteDialog = ref(false)

const props = defineProps<{
  item: components['schemas']['MediaitemPublic']
  image_number?: number
  images_total?: number
  showDownload: boolean
  showDelete: boolean
  showFilter: boolean
  showShare: boolean
  shareButtons: ShareSchema[]
  enableFilter: boolean
}>()

const emit = defineEmits<{
  triggerShareAction: [config_index: number]
  triggerToggleDisplayFilter: []
  triggerDeleteMediaitem: [id: string]
  closeEvent: [] // from quasar
}>()

function invokeShareAction(config_index: number) {
  emit('triggerShareAction', config_index)
}

function invokeToggleDisplayFilter() {
  emit('triggerToggleDisplayFilter')
}

function invokeDeleteMediaitem(id: string) {
  emit('triggerDeleteMediaitem', id)
}
</script>


<style lang="scss" scoped>
.delete-dialog {
  min-width: 600px;
  background: linear-gradient(180deg, rgba(26, 26, 166, 0.2) 0%, rgba(255, 255, 255, 0.2) 100%), #ffffff;
  border: 10px solid #000000 !important;
  border-radius: 62px !important;
  box-shadow: 10px 20px 0px 0px #000000;
  padding: 50px 60px;
  text-align: center;
  overflow: hidden;
}

.delete-caption {
  font-family: 'Kumbh Sans', 'Inter', sans-serif;
  font-weight: 500;
  font-size: 48px;
  line-height: 60px;
  color: #000000;
  margin-bottom: 40px;
}

.delete-actions {
  display: flex;
  justify-content: center;
  gap: 30px;
}

.delete-btn-no {
  width: 220px;
  height: 110px;
  background: linear-gradient(180deg, rgba(136, 136, 255, 0.5) 0%, rgba(197, 197, 255, 0) 100%), #ffffff;
  border: 8px solid #000000;
  border-radius: 50px;
  cursor: pointer;
  font-family: 'Kumbh Sans', 'Inter', sans-serif;
  font-weight: 700;
  font-size: 40px;
  letter-spacing: 0.05em;
  color: #000000;
  transition: filter 0.2s;

  &:hover {
    filter: brightness(0.95);
  }

  &:active {
    filter: brightness(0.9);
  }
}

.delete-btn-yes {
  width: 220px;
  height: 110px;
  background: linear-gradient(180deg, #da2436 0%, #ff9fa0 100%);
  border: 8px solid #000000;
  border-radius: 50px;
  box-shadow: 0px 0px 2px 8px rgba(255, 255, 255, 0.5);
  cursor: pointer;
  font-family: 'Kumbh Sans', 'Inter', sans-serif;
  font-weight: 700;
  font-size: 40px;
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
