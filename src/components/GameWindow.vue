<script setup lang="ts">
import { ref } from 'vue'
let start = new Date().getTime()
let help = ref(0)

let title = 'obhájati -am nedov. (ȃ)\n'
let descriptionUmodified = [
  'hoditi okrog česa : straža je celo noč obhajala tabor \n // izogibati se : ne obhajaj težav z raznimi izgovori ; pren. obhajati predpise \n // star. hoditi : ob progi so obhajale straže / rad obhaja tuje države obiskuje',
  'proslavljati kak pomemben dogodek ali spomin nanj ; praznovati : danes obhaja svoj šestnajsti rojstni dan ; novo leto bomo obhajali kar doma / zvečer so se zbrali in obhajali slovo dobrih prijateljev',
  'z oslabljenim pomenom izraža stanje , kot ga določa samostalnik : groza , strah me obhaja , kadarkoli ga zagledam / obhajale so ga čudne misli ; večkrat ga obhajajo hude slutnje',
  'rel. dati , deliti posvečene hostije : duhovnik je bolnika spovedal in obhajal'
]
type Word = {
  word: string
  hint: number
  guessed: EnumGuess
}
enum EnumGuess {
  'true',
  'false',
  'alwaysDisplay',
  'help'
}

let description = ref(
  descriptionUmodified.map((desc) => {
    return desc.split(' ').map<Word>((word) => {
      switch (word) {
        case ':':
        case ';':
        case '.':
        case ',':
        case '/':
        case '//':
        case '\n':
          return {
            word: word,
            hint: 0,
            guessed: EnumGuess.alwaysDisplay
          }
        case 'pren.':
        case 'star.':
        case 'rel.':
        case 'z':
        case 'oslabljenim':
        case 'pomenom':
          return {
            word: word,
            hint: 0,
            guessed: EnumGuess.help
          }
        default:
          return {
            word: word,
            hint: 0,
            guessed: EnumGuess.false
          }
      }
    })
  })
)

let input = ref('')

const submit = () => {
  let i = input.value.toLowerCase()
  description.value.forEach((sentence) =>
    sentence
      .filter((word) => word.word === i)
      .map((word) => (word.guessed = EnumGuess.true))
  )
  input.value = ''
  if (solved() === all()) {
    win()
  }
}
const win = () => {
  let time = new Date().getTime() - start
  let result = `Čas:
  ${Math.floor(time / (1000 * 60))} min
  ${Math.floor((time % (60 * 1000)) / 1000)} sec
  \n
  Točke: ${solved()}/${all()}
  Pomoči: ${help.value}
  `
  alert(result)
}
const hint = () => {
  help.value++
  description.value[help.value % 4].filter(
    (word) => word.guessed === EnumGuess.false && word.hint <= word.word.length
  )[
    Math.floor(
      Math.random() *
        description.value[help.value % 4].filter(
          (word) =>
            word.guessed === EnumGuess.false && word.hint <= word.word.length
        ).length
    )
  ].hint++
}
const solved = () => {
  let x = 0
  description.value.forEach(
    (sentence) =>
      (x += sentence.filter((word) => word.guessed === EnumGuess.true).length)
  )
  return x
}
const all = () => {
  let x = 0
  description.value.forEach(
    (sentence) =>
      (x += sentence.filter(
        (word) =>
          word.guessed === EnumGuess.true || word.guessed === EnumGuess.false
      ).length)
  )
  return x
}
</script>

<template>
  <header class="font-semibold my-2">{{ title }}</header>
  <p v-for="(sentence, i) in description" :key="i" class="whitespace-pre-wrap">
    {{ i + 1 }}.
    <template v-for="word in sentence" :key="word.word">
      <span
        v-if="
          word.guessed === EnumGuess.alwaysDisplay ||
          word.guessed === EnumGuess.true
        "
      >
        {{ word.word }}
      </span>
      <span v-else-if="word.guessed === EnumGuess.help" class="italic">
        {{ word.word }}
      </span>
      <span v-for="(letter, index) in word.word" v-else :key="letter">
        <span v-if="index < word.hint" class="text-teal-600">{{ letter }}</span>
        <span v-else>_</span>
      </span>
      {{ ' ' }}
    </template>
  </p>
  <footer class="flex mt-16">
    <input
      v-model="input"
      class="bg-yellow-50 border-2 border-gray-600 outline-none"
      autofocus
      @keyup.enter="submit"
    />
    <label class="mx-4 font-bold">{{ solved() }}/{{ all() }}</label>
    <button
      class="ml-auto mr-4 rounded bg-yellow-400 px-3 font-medium"
      @click="hint"
    >
      Hint
    </button>
  </footer>
</template>
