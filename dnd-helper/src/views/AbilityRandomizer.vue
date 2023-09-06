<template>
  <div>
    <h1>人物属性roll点工具</h1>
    <br />
    <div>
      随机数量(1-5): <input id="count" type="number" min="1" max="5" v-model="random_count" />
      &nbsp;
      <button @click="random_n_replica">随机</button>
    </div>
    <br />
    <div v-for="character in characters">
      <span v-for="(ability, index) in character">
        {{ ability_header[index] }}: {{ ability }} &nbsp;&nbsp;
      </span>
      <div>总和: {{ character.reduce((a, b) => a + b, 0) }}</div>
      <div>快速推荐职业: {{ suggest_class(character) }}</div>
      <hr />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'


const random_count = ref(1)
const random_result = ref('')
const ability_header = ref(['力量', '敏捷', '体质', '智力', '感知', '魅力'])
const characters = ref([])
const charachers_total = ref([])

const min_ability = 8
const min_ability_sum = 52
const max_ability = 17
const max_ability_sum = 96
const max_roll_round = 200

const suggest_class = (ability) => {
  let class_list = []
  let total = ability.reduce((a, b) => a + b, 0)
  // 大天才
  if (total >= 85) {
    class_list.push('你生来传奇，爱玩啥玩啥吧')
    return class_list
  }
  // 废物
  if (total < 60) {
    class_list.push('建议remake')
    return class_list
  }
  let ability_pair = [
    { name: 'str', value: ability[0] },
    { name: 'dex', value: ability[1] },
    { name: 'con', value: ability[2] },
    { name: 'int', value: ability[3] },
    { name: 'wis', value: ability[4] },
    { name: 'cha', value: ability[5] },
  ]
  let ability_map = {
    'str': ability[0],
    'dex': ability[1],
    'con': ability[2],
    'int': ability[3],
    'wis': ability[4],
    'cha': ability[5],
  }
  ability_pair.sort((a, b) => b.value - a.value)
  let first = ability_pair[0]
  let second = ability_pair[1]
  // artificer
  if (first.name == 'int' && second.name == 'dex') {
    class_list.push('奇械师')
  }
  if (first.name == 'int' && second.name == 'con') {
    class_list.push('奇械师')
  }
  // babarian
  if (first.name == 'str' && second.name == 'con') {
    class_list.push('野蛮人')
  }
  // bard
  if (first.name == 'cha') {
    class_list.push('吟游诗人')
  }
  if (first.name == 'cha' && second.name == 'dex') {
    class_list.push('吟游诗人')
  }
  if (ability_map['cha'] > 13 && (ability_map['dex'] > 15 || ability_map['int'] > 15)) {
    class_list.push('剑舞学院-吟游诗人')
  }
  // cleric
  if (first.name == 'wis') {
    class_list.push('牧师')
  }
  if (first.name == 'wis' && second.name == 'con') {
    class_list.push('牧师')
  }
  if (first.name == 'wis' && second.name == 'str') {
    class_list.push('牧师')
  }
  // druid
  if (first.name == 'wis' && second.name == 'dex') {
    class_list.push('德鲁伊')
  }
  if (first.name == 'wis' && second.name == 'str') {
    class_list.push('德鲁伊')
  }
  if (first.name == 'wis' && second.name == 'con') {
    class_list.push('德鲁伊')
  }
  // fighter
  if (first.name == 'str') {
    class_list.push('战士')
  }
  if (first.name == 'str' && second.name == 'con') {
    class_list.push('战士')
  }
  if (first.name == 'dex' && second.name == 'con') {
    class_list.push('战士')
  }
  if (((ability_map['str'] > 15) || (ability_map['dex'] > 15)) && ability_map['int'] > 13) {
    class_list.push('奥法骑士-战士')
  }
  // monk
  if (first.name == 'dex' && second.name == 'wis') {
    class_list.push('武僧')
  }
  // paladin
  if (first.name == 'str' && second.name == 'cha') {
    class_list.push('圣武士')
  }
  // ranger
  if (first.name == 'dex' && second.name == 'wis') {
    class_list.push('游侠')
  }
  // rogue
  if (first.name == 'dex') {
    class_list.push('游荡者')
  }
  if (ability_map['dex'] > 15 && ability_map['int'] > 13) {
    class_list.push('诡术师-游荡者')
  }
  if (ability_map['dex'] > 15 && ability_map['cha'] > 13) {
    class_list.push('策士-游荡者')
  }
  // sorcerer
  if (first.name == 'cha' && second.name == 'con') {
    class_list.push('术士')
  }
  // warlock
  if (first.name == 'cha' && second.name == 'con') {
    class_list.push('邪术师')
  }
  // wizard
  if (first.name == 'int' && second.name == 'dex') {
    class_list.push('法师')
  }
  // distinct
  class_list = [...new Set(class_list)]
  return class_list
}

const box_muller_random = (mean, stdev) => {
  let u = 0, v = 0;
  //Converting [0,1) to (0,1)
  while (u === 0) u = Math.random(); 
  while (v === 0) v = Math.random();
  // Translate to 0 -> 1
  let num = Math.sqrt(-2.0 * Math.log(u)) * Math.cos(2.0 * Math.PI * v);
  num = num / 10.0 + 0.5;
  // resample between 0 and 1
  if (num > 1 || num < 0) return box_muller_random(mean, stdev); 
  num *= stdev; // Stretch to fill range
  num += mean; // offset to mean
  return num;
}

const random_int = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1) + min)
}

const random_single_replica = () => {
  let mean_sum = (max_ability_sum + min_ability_sum) / 2
  let stdev_sum = (max_ability_sum - min_ability_sum) / 6
  let ability_sum = Math.floor(box_muller_random(mean_sum, stdev_sum))
  let character_ability = [0, 0, 0, 0, 0, 0]
  let roll_round = 0;
  let single_max = max_ability;
  let max_count = 0;
  while (ability_sum > 0) {
    let ability_index = random_int(0, 5)
    let min_roll = min_ability - character_ability[ability_index];
    let max_roll = single_max - character_ability[ability_index];
    min_roll = min_roll < 0 ? 0 : min_roll;
    max_roll = max_roll < 0 ? 0 : max_roll;
    max_roll = max_roll > ability_sum ? ability_sum : max_roll;
    if (min_roll > max_roll) {
      continue;
    }
    let roll = random_int(min_roll, max_roll)
    character_ability[ability_index] += roll
    if (character_ability[ability_index] > single_max - 2) {
      max_count += 1
    }
    if (max_count == 2) {
      single_max -= 2
      max_count = 0
    }
    ability_sum -= roll
    roll_round += 1
    if (roll_round > max_roll_round) {
      break;
    }
  }
  for (let i = 0; i < 6; i++) {
    if (character_ability[i] < min_ability) {
      character_ability[i] = min_ability
    }
  }
  return character_ability
}

const random_n_replica = () => {
  let result = []
  let result_total = []
  for (let i = 0; i < random_count.value; i++) {
    let character_ability = random_single_replica()
    let character_ability_total = character_ability.reduce((a, b) => a + b, 0)
    result.push(character_ability)
    result_total.push(character_ability_total)
  }
  characters.value = result
  charachers_total.value = result_total
}

</script>

<style>
@media (min-width: 1024px) {}
</style>
