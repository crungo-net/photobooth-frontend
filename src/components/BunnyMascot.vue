<template>
  <div class="bunny-mascot">
    <img src="../assets/bunny.png" alt="" class="bunny-image" />
    <div class="speech-bubble">
      <span class="speech-bubble-text">{{ $t('BUNNY_SPEECH_BUBBLE_TEXT') }}</span>
    </div>
  </div>
</template>

<script setup lang="ts"></script>

<style lang="scss" scoped>
/* ── Size configuration ──────────────────────────────────────
 * Adjust these custom properties to resize the mascot elements.
 *   --bunny-image-height : height of the bunny image
 *   --bubble-padding     : inner padding of the speech bubble
 *   --bubble-font-size   : font size of the speech bubble text
 *   --bubble-border-size : border thickness of the speech bubble
 *   --bubble-radius      : border radius of the speech bubble
 * ──────────────────────────────────────────────────────────── */
.bunny-mascot {
  --bunny-image-height: 300px;
  --bubble-padding: 20px 32px;
  --bubble-font-size: 32px;
  --bubble-border-size: 5px;
  --bubble-radius: 40px;

  position: fixed;
  bottom: 0;
  left: 0;
  display: flex;
  align-items: flex-end;
  gap: 0px;
  pointer-events: none;
  z-index: 1;
}

.bunny-image {
  height: var(--bunny-image-height);
  width: auto;
  transform: scaleX(-1);
  display: block;
}

.speech-bubble {
  position: relative;
  background: linear-gradient(180deg, rgba(26, 26, 166, 0.2) 0%, rgba(255, 255, 255, 0.2) 100%), #ffffff;
  border: var(--bubble-border-size) solid #000000;
  border-radius: var(--bubble-radius);
  padding: var(--bubble-padding);
  margin-bottom: 180px;
  margin-left: -95px;

  /* Tail pointing down-left toward the bunny */
  &::after {
    content: '';
    position: absolute;
    bottom: -24px;
    left: 20px;
    width: 0;
    height: 0;
    border-left: 6px solid transparent;
    border-right: 24px solid transparent;
    border-top: 24px solid #000000;
  }

  &::before {
    content: '';
    position: absolute;
    bottom: -16px;
    left: 23px;
    width: 0;
    height: 0;
    border-left: 4px solid transparent;
    border-right: 20px solid transparent;
    border-top: 18px solid #ffffff;
    z-index: 1;
  }
}

.speech-bubble-text {
  font-family: 'Kumbh Sans', 'Inter', sans-serif;
  font-weight: 500;
  font-size: var(--bubble-font-size);
  line-height: 1.3;
  color: #000000;
  white-space: nowrap;
}
</style>
