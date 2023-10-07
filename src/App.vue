<template>
    <main class="age-calculator">
        <div class="container">
            <section class="age-calculator__input-values">
                <div class="age-calculator__input-group">
                    <InputField
                        type="text"
                        id="day"
                        name="day"
                        v-model="day.inputValue"
                        :valid="day.valid"
                        :errorMessage="day.message"
                        label="Day"
                        placeholder="DD"
                        :maxLength="2"
                        @validate-change="validateInputFields"
                    />
                </div>
                <div class="age-calculator__input-group">
                    <InputField
                        type="text"
                        id="month"
                        name="month"
                        v-model="month.inputValue"
                        :valid="month.valid"
                        :errorMessage="month.message"
                        label="Month"
                        placeholder="MM"
                        :maxLength="2"
                        @validate-change="validateInputFields"
                    />
                </div>
                <div class="age-calculator__input-group">
                    <InputField
                        type="text"
                        id="year"
                        name="year"
                        v-model="year.inputValue"
                        :valid="year.valid"
                        :errorMessage="year.message"
                        label="Year"
                        placeholder="YYYY"
                        :maxLength="4"
                        @validate-change="validateInputFields"
                    />
                </div>
            </section>

            <section class="age-calculator-action">
                <button class="button" @click="calculateAge">
                    <IconArrow />
                </button>
            </section>

            <section class="calculated-age">
                <div class="years">
                    <span class="years__value">{{ years ? years : "--" }}</span> years
                </div>
                <div class="months">
                    <span class="months__value">{{ months === null ? `--` : months }}</span> months
                </div>
                <div class="days">
                    <span class="days__value">{{ days ? days : "--" }}</span> days
                </div>
            </section>
        </div>
    </main>
</template>
<script setup>
import {ref, watch} from "vue";
import InputField from "./components/InputField.vue";
import IconArrow from "./components/icons/icon-arrow.vue";

// Values for Calculations
const today = new Date();
const valid = ref(false);

const years = ref(0);
const months = ref(null);
const days = ref(0);

const calculated = ref(false);

// Error Messages
const day = ref({
    inputValue: "",
    message: "This field is required",
    valid: true,
});

const month = ref({
    inputValue: "",
    message: "This field is required",
    valid: true,
});

const year = ref({
    inputValue: "",
    message: "This field is required",
    valid: true,
});

const validateInputFields = async () => {
    await validateMonth();
    validateDay();
    validateYear();

    // Check if all Fields are Valid
    if (day.value.valid && month.value.valid && year.value.valid) {
        valid.value = true;
    } else {
        valid.value = false;
    }
};

const validateDay = () => {
    // Check if Day is Empty
    if (day.value.inputValue === "") {
        day.value.valid = false;
        day.value.message = "This field is required";
        return;
    } else if (day.value.inputValue < 1 || day.value.inputValue > 31) {
        day.value.valid = false;
        day.value.message = "Must be a valid month";
        return;
    } else if (day.value.inputValue !== "" && month.value.inputValue !== "") {
        // Check if Day is valid for Month
        const daysInMonth = new Date(year.value.inputValue, month.value.inputValue, 0).getDate();

        console.log(`Days in Month: ${daysInMonth}`);

        if (day.value.inputValue > daysInMonth) {
            day.value.valid = false;
            day.value.message = "Must be a valid day";
            return;
        } else {
            day.value.valid = true;
            return;
        }
    } else {
        day.value.valid = true;
    }
};

const validateMonth = async () => {
    // Check if Month is Empty
    if (month.value.inputValue === "") {
        month.value.valid = false;
        month.value.message = "This field is required";
        return;
    } else if (month.value.inputValue < 1 || month.value.inputValue > 12) {
        month.value.valid = false;
        month.value.message = "Must be a valid month";
        return;
    } else {
        month.value.valid = true;
        return;
    }
};

const validateYear = () => {
    // Check if Year is Empty and if it is a valid year that is in the past
    if (year.value.inputValue === "") {
        year.value.valid = false;
        year.value.message = "This field is required";
        return;
    } else if (year.value.inputValue > today.getFullYear()) {
        year.value.valid = false;
        year.value.message = "Must be a valid year";
        return;
    } else {
        year.value.valid = true;
        return;
    }
};

const calculateAge = async () => {
    await validateInputFields();

    if (!valid.value) {
        console.log("Not Valid");
        return;
    }

    // Calculate the number of years.value, months, and days.value, based on the inputted date
    const birthDate = new Date(`${year.value.inputValue}-${month.value.inputValue}-${day.value.inputValue}`);
    years.value = today.getFullYear() - birthDate.getFullYear();
    months.value = today.getMonth() - birthDate.getMonth();
    days.value = today.getDate() - birthDate.getDate();

    console.log(`Years: ${years.value}`);

    // If the current month is less than the birth month, subtract a year
    if (months.value < 0 || (months.value === 0 && today.getDate() < birthDate.getDate())) {
        years.value--;
    }

    // If the current month is less than the birth month, add 12 to the months
    if (months.value < 0) {
        months.value += 12;
        console.log(`Months: ${months.value}`);
    }

    // If the current day is less than the birth day, subtract a month
    if (days.value < 0) {
        months.value--;
    }

    // If the current day is less than the birth day, add the number of days in the birth month to the days.value
    if (days.value < 0) {
        const daysInMonth = new Date(today.getFullYear(), today.getMonth(), 0).getDate();
        days.value += daysInMonth;
    }

    console.log(`Years: ${years.value}`);

    // Display the age
    console.log(`You are ${years.value} years, ${months.value} months, and ${days.value} days old.`);

    console.log("Calculate Age");

    calculated.value = true;
};

// Watch Day for Changes
watch(day, (newValue) => {
    console.log("Day Changed", newValue);
    validateInputFields();
});
</script>
