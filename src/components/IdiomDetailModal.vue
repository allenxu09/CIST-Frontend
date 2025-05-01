<template>
  <transition name="modal-fade">
    <div
      v-if="show"
      class="fixed inset-0 bg-black/60 flex items-center justify-center p-4 z-50 backdrop-blur-sm"
      @click.self="$emit('close')"
    >
      <div
        class="bg-white/90 rounded-xl shadow-2xl max-w-lg w-full p-6 max-h-[90vh] overflow-y-auto m-4"
        role="dialog"
        aria-modal="true"
        :aria-labelledby="'idiom-title-' + (idiom?.word || 'modal')"
      >
        <!-- Modal Header -->
        <div class="flex justify-between items-start pb-3 border-b border-gray-200">
          <h3 :id="'idiom-title-' + (idiom?.word || 'modal')" class="text-2xl font-bold text-blue-800">{{ idiom?.word }}</h3>
          <button @click="$emit('close')" class="text-gray-400 hover:text-gray-600 transition p-1 rounded-full -mt-1 -mr-1" aria-label="关闭弹窗">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <!-- Modal Body -->
        <div class="mt-4 space-y-4 text-base">
          <div>
            <span class="font-semibold text-gray-600">拼音：</span>
            <span class="text-gray-800">{{ idiom?.pinyin || '无' }}</span>
          </div>
          <div>
            <span class="font-semibold text-gray-600 block mb-1">释义：</span>
            <p class="text-gray-800 leading-relaxed line-clamp-2">{{ idiom?.explanation || '无' }}</p>
          </div>
          <div v-if="idiom?.derivation">
            <span class="font-semibold text-gray-600 block mb-1">出处：</span>
            <p class="text-gray-800 italic leading-relaxed">{{ idiom.derivation }}</p>
          </div>
          <div v-if="idiom?.example">
            <span class="font-semibold text-gray-600 block mb-1">例句：</span>
            <p class="text-gray-800 leading-relaxed">"{{ idiom.example }}"</p>
          </div>
        </div>

        <!-- Modal Footer -->
        <div class="mt-6 pt-4 border-t border-gray-200 flex justify-end space-x-3">
          <button
            @click="handleCopy"
            class="border bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium px-4 py-2 rounded-lg transition text-sm flex items-center"
            :disabled="!idiom?.word"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
            </svg>
            复制成语
          </button>
          <button
            @click="$emit('close')"
            class="bg-blue-600 hover:bg-blue-700 text-white font-medium px-5 py-2 rounded-lg transition text-sm"
          >
            关闭
          </button>
        </div>
      </div>
      <!-- Toast Notification -->
      <transition name="toast-fade">
        <div v-if="showCopyNotification" class="fixed bottom-5 right-5 bg-green-600 text-white px-4 py-2 rounded-lg shadow-md">
          成语已复制!
        </div>
      </transition>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'IdiomDetailModal',
  props: {
    show: {
      type: Boolean,
      required: true
    },
    idiom: {
      type: Object,
      default: () => ({})
    }
  },
  emits: ['close', 'copy-idiom'],
  data() {
    return {
      showCopyNotification: false,
    };
  },
  methods: {
    handleCopy() {
      if (this.idiom?.word) {
        this.$emit('copy-idiom', this.idiom.word);
        this.showCopyNotification = true;
        setTimeout(() => {
          this.showCopyNotification = false;
        }, 2000);
      }
    },
    handleEscKey(event) {
      if (event.key === 'Escape') {
        this.$emit('close');
      }
    }
  },
  watch: {
    show(newValue) {
      if (newValue) {
        this.$nextTick(() => {
          window.addEventListener('keydown', this.handleEscKey);
        });
      } else {
        window.removeEventListener('keydown', this.handleEscKey);
      }
    }
  },
  beforeUnmount() {
    window.removeEventListener('keydown', this.handleEscKey);
  },
}
</script>

<style scoped>

.modal-fade-enter-active .bg-white, .modal-fade-leave-active .bg-white {
  transition: transform 0.3s ease;
}
.modal-fade-enter-from .bg-white, .modal-fade-leave-to .bg-white {
  transform: scale(0.95) translateY(10px);
}

</style>
