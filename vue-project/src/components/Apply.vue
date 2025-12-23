<template>
  <section class="max-w-lg px-4 py-20 mx-auto">
    <div
      class="bg-white border border-gray-100 rounded-[32px] p-8 md:p-10 shadow-2xl transition-all duration-300"
      style="opacity: 1 !important; visibility: visible !important"
    >
      <div
        v-if="hasApplied"
        class="py-10 text-center"
      >
        <div class="mb-4 text-5xl">✅</div>
        <p class="mb-2 text-xl font-bold text-slate-800">응모가 완료되었습니다!</p>
        <p class="text-sm text-slate-500">결과 발표를 기다려주세요.</p>
      </div>

      <form
        v-else
        @submit.prevent="submitForm"
        class="space-y-6"
      >
        <h3 class="mb-8 text-2xl font-bold text-center text-slate-900">이벤트 응모하기</h3>

        <div class="space-y-2">
          <label class="block ml-1 text-sm font-bold text-slate-700">이름</label>
          <input
            v-model="form.name"
            type="text"
            placeholder="성함을 입력해주세요"
            class="input-style"
            :class="{ 'border-red-500 focus:ring-red-500': errors.name }"
          />
          <p
            v-if="errors.name"
            class="ml-1 text-xs font-medium text-red-500"
          >
            {{ errors.name }}
          </p>
        </div>

        <div class="space-y-2">
          <label class="block ml-1 text-sm font-bold text-slate-700">연락처</label>
          <input
            v-model="form.phone"
            type="tel"
            @input="formatPhone"
            placeholder="010-0000-0000"
            maxlength="13"
            class="input-style"
            :class="{ 'border-red-500 focus:ring-red-500': errors.phone }"
          />
          <p
            v-if="errors.phone"
            class="ml-1 text-xs font-medium text-red-500"
          >
            {{ errors.phone }}
          </p>
        </div>

        <div class="space-y-2">
          <label class="block ml-1 text-sm font-bold text-slate-700">이메일</label>
          <input
            v-model="form.email"
            type="email"
            placeholder="example@email.com"
            class="input-style"
            :class="{ 'border-red-500 focus:ring-red-500': errors.email }"
          />
          <p
            v-if="errors.email"
            class="ml-1 text-xs font-medium text-red-500"
          >
            {{ errors.email }}
          </p>
        </div>

        <div class="p-4 bg-slate-50 rounded-2xl">
          <div class="flex items-center gap-3">
            <input
              v-model="form.agreedTerms"
              type="checkbox"
              id="terms"
              class="w-5 h-5 cursor-pointer accent-blue-600"
            />
            <label
              for="terms"
              class="text-sm font-medium cursor-pointer select-none text-slate-600"
            >
              개인정보 수집 및 이용에 동의합니다.
            </label>
          </div>
          <p
            v-if="errors.agreedTerms"
            class="mt-2 ml-1 text-xs font-medium text-red-500"
          >
            {{ errors.agreedTerms }}
          </p>
        </div>

        <button
          :disabled="isSubmitting"
          type="submit"
          class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-2xl shadow-lg shadow-blue-100 active:scale-[0.98] transition-all duration-200 disabled:bg-slate-300 disabled:cursor-not-allowed"
        >
          <span v-if="!isSubmitting">응모 완료하기</span>
          <span
            v-else
            class="flex items-center justify-center gap-2"
          >
            <svg
              class="w-5 h-5 text-white animate-spin"
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
  import { ref, reactive, onMounted, nextTick } from "vue";

  const isSubmitting = ref(false);
  const hasApplied = ref(false);

  const form = ref({
    name: "",
    phone: "",
    email: "",
    agreedTerms: false,
  });

  const errors = reactive<Record<string, string>>({
    name: "",
    phone: "",
    email: "",
    agreedTerms: "",
  });

  onMounted(() => {
    if (localStorage.getItem("hasApplied") === "true") {
      hasApplied.value = true;
    }
  });

  const validate = (): boolean => {
    errors.name = form.value.name.trim().length < 2 ? "이름을 2자 이상 입력해주세요." : "";
    errors.phone = !/^01([0|1|6|7|8|9])-?([0-9]{3,4})-?([0-9]{4})$/.test(form.value.phone)
      ? "번호 형식을 확인해주세요."
      : "";
    errors.email = !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email)
      ? "이메일 형식을 확인해주세요."
      : "";
    errors.agreedTerms = !form.value.agreedTerms ? "필수 동의 항목입니다." : "";

    return !Object.values(errors).some((msg) => msg !== "");
  };

  const formatPhone = (e: Event) => {
    const target = e.target as HTMLInputElement;
    let val = target.value.replace(/\D/g, "");

    if (val.length <= 11) {
      if (val.length > 7) {
        val = val.replace(/(\d{3})(\d{4})(\d{4})/, "$1-$2-$3");
      } else if (val.length > 3) {
        val = val.replace(/(\d{3})(\d{1,4})/, "$1-$2");
      }
    }
    form.value.phone = val.slice(0, 13);
  };

  const submitForm = async () => {
    if (!validate()) return;
    isSubmitting.value = true;

    try {
      const res = await fetch("https://694a308c1282f890d2d7db97.mockapi.io/entries", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(form.value),
      });

      if (res.ok) {
        localStorage.setItem("hasApplied", "true");
        hasApplied.value = true;

        await nextTick();
        const el = document.querySelector(".max-w-lg");
        if (el) el.scrollIntoView({ behavior: "smooth", block: "center" });
      }
    } catch (err) {
      alert("오류가 발생했습니다. 다시 시도해주세요.");
    } finally {
      isSubmitting.value = false;
    }
  };
</script>

<style scoped>
  @import "tailwindcss";

  .input-style {
    @apply w-full bg-gray-50 border border-gray-200 rounded-2xl px-5 py-4 text-slate-900 placeholder-gray-400 focus:outline-none focus:ring-4 focus:ring-blue-50 focus:border-blue-500 transition-all;
  }
</style>
