<script setup lang="ts">
  import { ref, onMounted, nextTick, computed } from "vue";
  import { gsap } from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import axios from "axios";

  gsap.registerPlugin(ScrollTrigger);

  interface Reward {
    id: number;
    name: string;
    image: string;
  }

  interface EventData {
    title: string;
    endDate: string;
    description: string;
    rewards: Reward[];
  }

  const initAnimations = () => {
    gsap.utils.toArray<HTMLElement>(".reveal").forEach((el) => {
      gsap.from(el, {
        scrollTrigger: {
          trigger: el,
          start: "top 85%",
        },
        y: 40,
        opacity: 0,
        duration: 1,
        ease: "power2.out",
      });
    });
  };

  const eventInfo = ref<EventData | null>(null);

  const fetchEventData = async () => {
    try {
      const url = "https://694a308c1282f890d2d7db97.mockapi.io/event";
      const response = await axios.get(url);

      //이벤트는 1개로 가정
      eventInfo.value = response.data[0];

      console.log(response.data);

      await nextTick();
      initAnimations();
    } catch (error) {
      console.error("데이터 로드 실패:", error);
      alert("이벤트 정보를 불러오는데 실패했습니다. 네트워크 상태를 확인해주세요.");
    }
  };

  const rewards = computed(() => {
    return eventInfo.value?.rewards || [];
  });

  onMounted(() => {
    fetchEventData();
    gsap.from(".reward-card", {
      scrollTrigger: {
        trigger: ".reward-grid",
        start: "top 85%",
        toggleActions: "play none none none",
      },
      y: 30,
      opacity: 0,
      duration: 0.6,
      stagger: 0.2,
      ease: "power2.out",
      clearProps: "opacity",
    });
  });
</script>

<template>
  <section class="py-24 px-6 max-w-6xl mx-auto bg-gray-50">
    <div class="text-center mb-16">
      <h2 class="text-3xl md:text-5xl font-black mb-5 text-slate-900 tracking-tight">
        특별한 혜택을 놓치지 마세요
      </h2>
      <p class="text-slate-600 text-lg font-medium">
        이벤트에 참여하신 분들을 위한 선물이 준비되어 있습니다.
      </p>
    </div>

    <div class="reward-grid grid grid-cols-1 md:grid-cols-3 gap-8">
      <div
        v-for="reward in rewards"
        :key="reward.id"
        class="reward-card group bg-white border border-gray-200 rounded-3xl p-8 transition-all duration-300 shadow-sm hover:shadow-xl"
      >
        <div class="relative z-10">
          <h3 class="text-2xl font-bold mb-3 text-slate-900">
            {{ reward.name }}
          </h3>
          <img :src="reward.image ? `/${reward.image}` : '/unknown.png'" />
        </div>

        <div
          class="absolute top-6 right-6 text-sm font-black font-mono text-gray-200 group-hover:text-gray-400 transition-colors"
        >
          0{{ reward.id }}
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
  .reward-card {
    will-change: transform;
  }

  .reward-card:hover {
    transform: translateY(-8px);
    border-color: #3b82f6;
  }
</style>
