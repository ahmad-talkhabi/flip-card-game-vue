<script setup>
import FlipGameItem from './icons/IconNounTick.vue'

const props = defineProps(['item'])
const emits = defineEmits(['show'])

function showItem() {
  if (props.item.detect || props.item.show) return
  emits('show')
}
</script>

<template>
  <div
      class="flip-card"
      @click="showItem"
  >
    <div
        class="flip-card__inner"
        :class="{
          flipped: props.item.detect || props.item.show,
          detect: props.item.detect
        }"
    >
      <div class="flip-card__face flip-card__face-front">
        {{ props.item.index }}
      </div>
      <div class="flip-card__face flip-card__face-back">
        <img
            :src="props.item.img"
            class="flip-card__image"
            alt="product"
        >
        <FlipGameItem class="flip-card__icon"/>
      </div>
    </div>
  </div>
</template>

<style lang="scss">

.flip-card {
  perspective: 300px;

  &__inner {
    width: 80px;
    height: 80px;
    position: relative;
    transition: transform 0.5s ease;
    transform-style: preserve-3d;
    cursor: pointer;

    &.detect {
      cursor: not-allowed;

      .flip-card__icon {
        opacity: 1;
        animation: pulse 0.5s ease-in-out;
      }
    }

    &.flipped {
      transform: rotateY(180deg);
    }
  }

  &__image {
    width: 100%;
    height: 100%;
    aspect-ratio: 1;
    border-radius: 10px;
    object-fit: cover;
  }

  &__face {
    position: absolute;
    height: 100%;
    width: 100%;
    left: 0;
    top: 0;
    backface-visibility: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &__face-back {
    transform: rotateY(180deg);
  }

  &__face-front {
    background: #ff637e;
    border-radius: 10px;
    color: white;
  }

  &__icon {
    color: #00bd36;
    width: 40px;
    height: 40px;
    opacity: 0;
    position: absolute;
  }
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(2);
  }
  100% {
    transform: scale(1);
  }
}

</style>
