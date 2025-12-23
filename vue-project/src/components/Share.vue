<template>
  <div class="flex justify-center mt-10">
    <button
      @click="shareLink"
      class="bg-blue-600 px-6 py-2 rounded-full font-bold hover:bg-blue-500 transition-colors text-white"
    >
      이벤트 공유하기
    </button>
  </div>
</template>

<script setup lang="ts">
  import { gsap } from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  const shareLink = async () => {
    try {
      await navigator.clipboard.writeText(window.location.href);
      alert("이벤트 링크가 복사되었습니다! 🔗");
    } catch (err) {
      alert("링크 복사에 실패했습니다.");
    }
  };
</script>
