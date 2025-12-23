<template>
  <section class="max-w-4xl mx-auto py-24 px-6 text-center">
    <h2 class="text-3xl font-black text-slate-900 mb-2">행운의 카드 뽑기</h2>
    <p class="text-slate-500 mb-12">세 개의 카드 중 하나를 클릭하여 당첨 확률을 확인하세요!</p>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-8 perspective-1000">
      <div
        v-for="n in 3"
        :key="n"
        class="card-container h-64 cursor-pointer"
        @click="handleFlip(n)"
      >
        <div
          class="card-inner w-full h-full transition-transform duration-700 preserve-3d"
          :class="{ 'is-flipped': flippedCard === n }"
        >
          <div
            class="card-front absolute inset-0 backface-hidden flex items-center justify-center bg-gradient-to-br from-blue-600 to-indigo-700 rounded-3xl border-4 border-white shadow-xl"
          >
            <span class="text-6xl text-white/50 font-black italic">?</span>
          </div>

          <div
            class="card-back absolute inset-0 backface-hidden rotate-y-180 flex flex-col items-center justify-center bg-white rounded-3xl border-4 border-blue-500 shadow-2xl"
          >
            <span class="text-5xl mb-4">🎁</span>
            <p class="text-blue-600 font-black text-xl">당첨 확률 UP!</p>
            <p class="text-slate-400 text-sm mt-2">응모 폼을 작성해 주세요</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
  import { ref } from "vue";
  import { gsap } from "gsap";

  const flippedCard = ref<number | null>(null);

  const handleFlip = (n: number) => {
    if (flippedCard.value !== null) return;

    flippedCard.value = n;

    gsap.to(`.card-container:nth-child(${n})`, {
      scale: 1.05,
      duration: 0.2,
      yoyo: true,
      repeat: 1,
      ease: "power2.inOut",
    });
  };
</script>

<style scoped>
  .perspective-1000 {
    perspective: 1000px;
  }

  .preserve-3d {
    transform-style: preserve-3d;
  }

  .backface-hidden {
    backface-visibility: hidden;
  }

  .rotate-y-180 {
    transform: rotateY(180deg);
  }

  .card-inner {
    position: relative;
  }

  .card-inner.is-flipped {
    transform: rotateY(180deg);
  }

  .card-container {
    transition: transform 0.3s ease;
  }

  .card-container:hover {
    transform: translateY(-10px);
  }
</style>
