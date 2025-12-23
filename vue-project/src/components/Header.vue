<template>
  <div class="min-h-screen font-sans">
    <div class="aurora-bg"></div>

    <section class="flex flex-col items-center justify-center min-h-screen px-4 text-center">
      <span
        class="px-4 py-1 mb-6 text-sm font-semibold tracking-wider text-blue-400 uppercase border border-blue-400/30 rounded-full bg-blue-400/10"
      >
        New Service Launch
      </span>
      <h1 class="mb-6 text-5xl font-black md:text-7xl leading-tight">
        신규 서비스 오픈 기념 <br />
        <span class="text-transparent bg-clip-text bg-gradient-to-r from-blue-400 to-purple-500">
          특별 혜택 이벤트
        </span>
      </h1>

      <div class="flex gap-4 mt-8">
        <div
          v-for="(val, unit) in timeLeft"
          :key="unit"
          class="flex flex-col items-center"
        >
          <div
            class="w-20 h-20 flex items-center justify-center text-3xl font-mono font-bold bg-white/10 backdrop-blur-xl rounded-xl border border-white/20 shadow-xl"
          >
            {{ String(val).padStart(2, "0") }}
          </div>
          <span class="mt-2 text-xs text-gray-400 uppercase">{{ unit }}</span>
        </div>
      </div>
      <Share></Share>
    </section>
  </div>
</template>

<script setup>
  import { ref, onMounted } from "vue";
  import { gsap } from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import Share from "./Share.vue";

  gsap.registerPlugin(ScrollTrigger);

  onMounted(() => {
    gsap.from(".reveal", {
      y: 50,
      opacity: 0,
      duration: 1,
      stagger: 0.2,
      scrollTrigger: {
        trigger: ".reveal",
        start: "top 85%",
      },
    });

    gsap.from(".hero-title", {
      scale: 0.9,
      opacity: 0,
      duration: 1.5,
      ease: "power2.out",
    });
  });

  const timeLeft = ref({ days: 0, hours: 0, minutes: 0, seconds: 0 });
  const targetDate = new Date("2025-12-31T23:59:59").getTime();

  const updateTimer = () => {
    const now = new Date().getTime();
    const diff = targetDate - now;
    timeLeft.value = {
      days: Math.floor(diff / (1000 * 60 * 60 * 24)),
      hours: Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)),
      minutes: Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60)),
      seconds: Math.floor((diff % (1000 * 60)) / 1000),
    };
  };

  onMounted(() => {
    setInterval(updateTimer, 1000);
  });
</script>
