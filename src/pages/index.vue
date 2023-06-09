<script setup lang="ts">
defineOptions({
  name: 'IndexPage',
})

interface Hitokoto {
  id: number
  hitokoto: string
  from: string
}

let isCopyDone = $ref(false)

const api = 'https://v1.hitokoto.cn'
const urlParams = new URLSearchParams({
  c: 'i',
  encode: 'json',
})

const { data, execute } = useFetch(`${api}?${urlParams}`).get().json<Hitokoto>()

const reference = $computed(() => {
  if (data.value?.from) {
    if (data.value.from.includes('·')) {
      const [left, right] = data.value.from.split('·')
      return `《${left} · ${right}》`
    }
    else { return `《${data.value.from}》` }
  }

  return ''
})

function onClick() {
  if (!data.value)
    return

  navigator.clipboard.writeText(data.value.hitokoto)

  if (!isCopyDone) {
    isCopyDone = true
    setTimeout(() => {
      isCopyDone = false
    }, 1800)
  }
}
</script>

<template>
  <div h-full flex items-center justify-center>
    <div>
      <div
        w-auto px-4 py-0.5 cursor-pointer rounded-md
        lg:text-14 md:text-10 sm:text-8
        transition transition-all duration-300
        class="hover:bg-zinc2/20"
        @click="onClick"
      >
        <Transition name="fit" mode="out-in">
          <div v-if="!isCopyDone">
            <TheSentence>
              <span
                text-shadow font-bold
                class="sentence"
              >
                {{ data?.hitokoto }}
              </span>
            </TheSentence>
          </div>
          <template v-else>
            <div i-carbon-ai-status-complete class="lg:w-10 lg:h-10 sm:w-5 sm:h-5 inline-block" />
          </template>
        </Transition>
      </div>

      <div my-2 />

      <div lg:text-6 text-4 class="text-amber/70 reflex">
        <TheSentence>
          <span tracking-widest class="sentence reflex">{{ reference }}</span>
        </TheSentence>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sentence {
  --at-apply: antialiased text-center;
}

.fit-enter-active,
.fit-leave-active {
  transition: all 0.3s ease;
}

.fit-enter-from,
.fit-leave-to {
  opacity: 0;
  transform: scale(0.7);
}

.reflex {
  -webkit-box-reflect: below 2px linear-gradient(transparent, #0004);
}
</style>
