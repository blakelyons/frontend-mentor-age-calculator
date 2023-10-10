<template>
    <label for="day" :class="`label ${props.valid ? `` : `error`}`">{{ props.label }}</label>
    <input
        :type="props.type"
        :id="props.id"
        :name="props.id"
        :aria-label="props.id"
        :value="modelValue"
        :placeholder="placeholder"
        :class="props.valid ? `` : `error`"
        @input="updateInput"
        @keyup="updateChangeInput"
        :maxlength="props.maxLength"
        ref="inputFieldRef"
    />
    <div :class="`error-message error ${!props.valid ? (hideErrorMessage ? `error-message--hide` : `error-message--show`) : ``}`">
        <div v-if="!props.valid">{{ props.errorMessage }}</div>
        <template v-else>&nbsp;</template>
    </div>
</template>

<script setup>
import {ref, watch, computed} from "vue";

const emit = defineEmits(["update:modelValue", "validateChange"]);

const props = defineProps({
    id: {
        type: String,
        required: true,
    },
    type: {
        type: String,
        required: true,
    },
    name: {
        type: String,
        required: true,
    },
    label: {
        type: String,
        required: true,
    },
    placeholder: {
        type: String,
        required: true,
    },
    modelValue: {
        type: [String, Number],
        default: "",
    },
    valid: {
        type: Boolean,
        default: false,
    },
    errorMessage: {
        type: String,
        default: "This field is required",
    },
    showError: {
        type: Boolean,
        default: false,
    },
    maxLength: {
        type: Number,
    },
    shouldFocus: {
        type: Boolean,
    },
});

// Add a ref to the input field
const inputFieldRef = ref(null);

const updateChangeInput = () => {
    emit("validateChange");
};

const updateInput = (event) => {
    emit("update:modelValue", event.target.value);
};

// Convert shouldFocus into a computed property
const shouldFocusComputed = computed(() => props.shouldFocus);

// Use the computed property in the watch
watch(shouldFocusComputed, (newVal) => {
    if (newVal) {
        inputFieldRef.value.focus();
    }
});

const hideErrorMessage = ref(false);

// Watch props.errorMessage for changes to animate error message in and out
watch(
    () => props.errorMessage,
    (oldVal, newVal) => {
        if (oldVal !== newVal) {
            hideErrorMessage.value = true;
            setTimeout(() => {
                hideErrorMessage.value = false;
            }, 100);
        }
    }
);
</script>
