<template>
  <input
    type="text"
    v-model="formattedValue"
    @input="onInput"
    @keydown="validateInput"
    placeholder="(123)456-7890"
  />
</template>

<script setup>
import { ref, nextTick } from "vue";

// Store the raw unformatted value
const rawValue = ref("");
// Store the formatted value for display
const formattedValue = ref("");

/**
 * Formats a numeric string into (123)456-7890 format.
 */
const formatPhoneNumber = (value) => {
  const digits = value.replace(/\D/g, ""); // Remove all non-numeric characters
  let formatted = "";

  if (digits.length >= 1) formatted += digits.substring(0, 3); // First 3 digits
  if (digits.length > 3) formatted = `(${formatted.substring(0, 3)})${digits.substring(3, 6)}`; // Add parentheses
  if (digits.length > 6) formatted += `-${digits.substring(6, 10)}`; // Add hyphen

  return formatted;
};

/**
 * Adjusts caret position after formatting the input.
 */
 const adjustCaretPosition = (event, oldRawValue, newRawValue) => {
  const inputElement = event.target;
  const rawCaretPosition = inputElement.selectionStart;

  let adjustedPosition = rawCaretPosition;
  const rawDiff = newRawValue.length - oldRawValue.length;

  // Handle special characters positions for formatting
  if (rawDiff > 0) {
    // Jump over the `)` after the first 3 digits
    if (rawCaretPosition === 4) adjustedPosition++; // Skip over the `)`
    // Jump over the `-` after the 6th digit
    if (rawCaretPosition === 9) adjustedPosition++;
  }

  // Set the new caret position
  nextTick(() => {
    inputElement.setSelectionRange(adjustedPosition, adjustedPosition);
  });
};

/**
 * Handles input changes, updates formatting, and adjusts caret position.
 */
 const onInput = (event) => {
  const inputElement = event.target;
  const input = inputElement.value;

  const oldRaw = rawValue.value; // Store previous raw value
  const newRaw = input.replace(/\D/g, ""); // Extract digits

  // Update values
  rawValue.value = newRaw;
  const oldFormatted = formattedValue.value;
  formattedValue.value = formatPhoneNumber(newRaw);

  // Calculate and adjust caret position
  const caretPosition = inputElement.selectionStart;
  const adjustedCaret = adjustCaretPosition(caretPosition, oldRaw, newRaw, oldFormatted);
  setTimeout(() => {
    inputElement.setSelectionRange(adjustedCaret, adjustedCaret);
  });
};

/**
 * Prevents invalid input (only digits and valid keys allowed).
 */
const validateInput = (event) => {
  const validKeys = ["ArrowLeft", "ArrowRight", "Backspace", "Delete"];
  if (!event.key.match(/[\d]/) && !validKeys.includes(event.key)) {
    event.preventDefault();
  }
};
</script>
