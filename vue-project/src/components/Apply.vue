<template>
  <section class="max-w-lg mx-auto px-4 py-20 reveal">
    <div class="bg-white border border-gray-100 rounded-[32px] p-8 md:p-10 shadow-2xl">
      <div
        v-if="hasApplied"
        class="text-center py-10"
      >
        <div class="text-5xl mb-4">✅</div>
        <p class="text-slate-800 font-bold text-xl mb-2">응모 완료되었습니다!</p>
        <p class="text-slate-500 text-sm">결과 발표를 기다려주세요.</p>
      </div>

      <form
        v-else
        @submit.prevent="submitForm"
        class="space-y-6"
      >
        <h3 class="text-2xl font-bold mb-8 text-slate-900 text-center">이벤트 응모하기</h3>
        <div class="space-y-2">
          <label class="block text-sm font-bold text-slate-700 ml-1">이름</label>
          <input
            v-model="form.name"
            type="text"
            placeholder="성함을 입력해주세요"
            class="input-style"
            :class="{ 'border-red-500 focus:ring-red-100': errors.name }"
          />
          <p
            v-if="errors.name"
            class="text-red-500 text-xs ml-1 font-medium"
          >
            {{ errors.name }}
          </p>
        </div>

        <div class="space-y-2">
          <label class="block text-sm font-bold text-slate-700 ml-1">연락처</label>
          <input
            v-model="form.phone"
            type="tel"
            @input="formatPhone"
            placeholder="010-0000-0000"
            maxlength="13"
            class="input-style"
            :class="{ 'border-red-500 focus:ring-red-100': errors.phone }"
          />
          <p
            v-if="errors.phone"
            class="text-red-500 text-xs ml-1 font-medium"
          >
            {{ errors.phone }}
          </p>
        </div>

        <div class="space-y-2">
          <label class="block text-sm font-bold text-slate-700 ml-1">이메일</label>
          <input
            v-model="form.email"
            type="email"
            placeholder="example@email.com"
            class="input-style"
            :class="{ 'border-red-500 focus:ring-red-100': errors.email }"
          />
          <p
            v-if="errors.email"
            class="text-red-500 text-xs ml-1 font-medium"
          >
            {{ errors.email }}
          </p>
        </div>

        <div class="bg-gray-50 p-4 rounded-2xl">
          <div class="flex items-center gap-3">
            <input
              v-model="form.agreedTerms"
              type="checkbox"
              id="terms"
              class="w-5 h-5 accent-blue-600 cursor-pointer"
            />
            <label
              for="terms"
              class="text-sm text-slate-600 font-medium cursor-pointer select-none"
            >
              개인정보 수집 및 이용에 동의합니다.
            </label>
          </div>
          <p
            v-if="errors.agreedTerms"
            class="text-red-500 text-xs mt-2 ml-1 font-medium"
          >
            {{ errors.agreedTerms }}
          </p>
        </div>

        <button
          :disabled="isSubmitting"
          type="submit"
          class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-lg shadow-blue-200 active:scale-[0.98] transition-all disabled:bg-gray-300 disabled:shadow-none disabled:cursor-not-allowed"
        >
          <span v-if="!isSubmitting">응모 완료하기</span>
          <span
            v-else
            class="flex items-center justify-center gap-2"
          >
            <svg
              class="animate-spin h-5 w-5 text-white"
              viewBox="0 0 24 24"
            >
              <circle
                class="opacity-25"
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                stroke-width="4"
                fill="none"
              ></circle>
              <path
                class="opacity-75"
                fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              ></path>
            </svg>
            처리 중...
          </span>
        </button>
      </form>
    </div>
  </section>
</template>

<script setup lang="ts">
  import { ref, reactive, onMounted } from "vue";
  import { gsap } from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  interface FormState {
    name: string;
    phone: string;
    email: string;
    agreedTerms: boolean;
  }

  interface ErrorState {
    name?: string;
    phone?: string;
    email?: string;
    agreedTerms?: string;
  }

  const isSubmitting = ref(false);
  const hasApplied = ref(false);

  const form = ref<FormState>({
    name: "",
    phone: "",
    email: "",
    agreedTerms: false,
  });

  const errors = reactive<ErrorState>({});

  onMounted(() => {
    if (localStorage.getItem("hasApplied") === "true") {
      hasApplied.value = true;
    } else {
      gsap.from(".reveal", {
        scrollTrigger: {
          trigger: ".reveal",
          start: "top 85%",
          toggleActions: "play none none none",
        },
        y: 30,
        opacity: 0,
        duration: 1,
        ease: "power2.out",
        clearProps: "all",
      });
    }
  });

  const formatPhone = (e: Event) => {
    const target = e.target as HTMLInputElement;
    const cleaned = target.value.replace(/\D/g, "");
    form.value.phone = cleaned.replace(/^(\d{2,3})(\d{3,4})(\d{4})$/, "$1-$2-$3").slice(0, 13);
  };

  const validate = (): boolean => {
    Object.keys(errors).forEach((key) => (errors[key as keyof ErrorState] = ""));
    let isValid = true;

    if (form.value.name.trim().length < 2) {
      errors.name = "이름을 2자 이상 입력해주세요.";
      isValid = false;
    }

    const phoneRegex = /^01([0|1|6|7|8|9])-?([0-9]{3,4})-?([0-9]{4})$/;
    if (!phoneRegex.test(form.value.phone)) {
      errors.phone = "올바른 휴대폰 번호를 입력해주세요.";
      isValid = false;
    }

    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(form.value.email)) {
      errors.email = "올바른 이메일 형식이 아닙니다.";
      isValid = false;
    }

    if (!form.value.agreedTerms) {
      errors.agreedTerms = "개인정보 수집 동의가 필요합니다.";
      isValid = false;
    }

    return isValid;
  };

  const submitForm = async () => {
    if (hasApplied.value) return;
    if (!validate()) return;

    isSubmitting.value = true;

    try {
      const res = await fetch("https://694a308c1282f890d2d7db97.mockapi.io/entries", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(form.value),
      });

      if (res.ok) {
        gsap.to(".reveal", {
          scale: 1.05,
          duration: 0.2,
          yoyo: true,
          repeat: 1,
          ease: "power2.inOut",
        });
        alert("🎉 응모가 완료되었습니다!");
        localStorage.setItem("hasApplied", "true");
        hasApplied.value = true;
      }
    } catch (err) {
      alert("오류가 발생했습니다. 다시 시도해주세요.");
    } finally {
      isSubmitting.value = false;
    }
  };
</script>

<style scoped>
  @reference "../assets/main.css";

  .input-style {
    @apply w-full bg-gray-50 border border-gray-200 rounded-2xl px-5 py-4 text-slate-900 placeholder-gray-400 focus:outline-none focus:ring-4 focus:ring-blue-50 focus:border-blue-500 transition-all;
  }
</style>
