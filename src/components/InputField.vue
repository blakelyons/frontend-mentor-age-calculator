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
    />
    <div class="error-message error">
        {{ props.valid ? "&nbsp;" : props.errorMessage }}
    </div>
</template>

<script setup>
//import {ref} from "vue";

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
    maxLength: {
        type: Number,
    },
});

const updateChangeInput = () => {
    emit("validateChange");
};

const updateInput = (event) => {
    emit("update:modelValue", event.target.value);
};
</script>
