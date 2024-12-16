<template>
    <input
        type="text"
        maxlength="13"
        :value="formattedPhoneNumber"
        @input="onInput"
        @keydown="validateInput"
        />
    <p>{{ formattedPhoneNumber }}</p>
</template>
<script setup>
import { ref } from 'vue'

const rawValue = ref("")
const formattedPhoneNumber = ref("")

function onInput(e) {
    rawValue.value = e.target.value
    formattedPhoneNumber.value = formatPhoneNumber(rawValue.value)
}
function formatPhoneNumber(val) {
    const digits = val.replace(/\D/g, "")
    let formatted = ""

    if (digits.length >= 1) formatted += digits.substring(0,3);
    if (digits.length > 3) formatted = `(${formatted.substring(0,3)})${digits.substring(3,6)}`
    if (digits.length > 6) formatted += `-${digits.substring(6,10)}`
    
    return formatted
}
function validateInput(e) {
    const validKeys = ["Backspace", "ArrowLeft", "ArrowRight"]
    if(!e.key.match(/^\d+$/) && !validKeys.includes(e.key)) {
        e.preventDefault()
    }
}
</script>