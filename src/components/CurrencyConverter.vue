<template>
  <div>
    <input
      :class="$style.input"
      @input="valueHandler(($event.target as HTMLInputElement).value)"
      @keypress="press" v-model="value"
    >EUR
  </div>
  <div>You will receive with bonus</div>
  <div>
    <input
      :class="$style.input"
      @input="valueReceiveHandle(($event.target as HTMLInputElement).value)"
      v-model="valueReceive"
      @keypress="press"
    >USD
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';

const props = withDefaults(
  defineProps<
    Partial<{
      rate: number;
      bonusPercent: number;
    }>
    & {
      modelValue: number;
    }
  >()
  ,
  {
    rate: 1,
    bonusPercent: 0
  }
)

type ConverterType = {
  value: number;
  rate: number;
}

type BonusType = {
  value: number;
  percent: number;
}

const emits = defineEmits<{
  'update:modelValue': [value: number]
}>()

const value = ref<string | number>(0)
const valueReceive = ref<string | number>(0)

const convert = (value: number) => {
  return  Number(value.toFixed(2))
}

const convertTo = ({ value, rate }: ConverterType) => {
  return convert(value * rate)
}

const convertFrom = ({ value, rate }: ConverterType) => {
  return convert(value / rate)
}

const calculateBonus = ({value, percent}: BonusType) => {
  return value + (value * (percent / 100))
}

const subtractBonus = ({ value, percent }: BonusType) => {
  return value / (1 + percent / 100)
}

const press = (e: KeyboardEvent) => {
  const onlyDigitsAndDots = /^[0-9]*\.?[0-9]*$/
  if (!onlyDigitsAndDots.test(e.key)) e.preventDefault()
}

const valueReceiveHandle = (_value: number | string) => {
  value.value = convertFrom({
    value: subtractBonus({value: Number(_value), percent: props.bonusPercent}),
    rate: props.rate
  })
}

const valueHandler = (value: number | string) => {
  const _value = props.bonusPercent ? calculateBonus({
    value: Number(value), percent: props.bonusPercent
  }) : Number(value)
  valueReceive.value = convertTo({ value: _value, rate: props.rate })
}

watch(() => props.modelValue, (_value: number) => {
  emits('update:modelValue', _value)
  value.value = _value
  valueHandler(_value)
}, {
  immediate: true
})
</script>

<style module>
.input {
  border: solid 2px #cdcdcd;
  padding: 10px 15px;
  margin: 10px;
  font-size: 16px;
}
</style>
