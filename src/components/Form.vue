<template>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-8 offset-md-2">
                <h1 class="text-center">User Information Form</h1>
                <form @submit.prevent="submitForm">
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label for="username" class="form-label">Username:</label><br>
                            <input type="text" id="username" class="form-control" @blur="() => validateName(true)"
                                @input="() => validateName(false)" v-model="formData.username"><br>
                            <div v-if="errors.username" class="text-danger">{{ errors.username }}</div>
                        </div>
                        <div class="col-md-6">
                            <label for="password" class="form-label">Password:</label><br>
                            <input type="password" id="password" name="password" class="form-control"
                                @blur="() => validatePassword(true)" @input="() => validatePassword(false)"
                                v-model="formData.password"><br>
                            <div v-if="errors.password" class="text-danger">{{ errors.password }}</div>
                        </div>
                    </div>
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <div class="form-check">
                                <input type="checkbox" id="isAustralian" name="isAustralian" class="form-check-input"
                                    v-model="formData.isAustralian"><br>
                                <label class="form-check-label" for="isAustralian" @blur="() => validateResident(true)"
                                    @input="() => validateResident(false)">Australian Resident?</label><br>
                                <div v-if="errors.resident" class="text-danger">{{ errors.resident }}</div>
                            </div>
                        </div>
                        <div class="col-md-6">
                            <label for="gender" class="form-label">Gender</label><br>
                            <select id="gender" class="form-select" @blur="() => validateGender(true)"
                                @input="() => validateGender(false)" v-model="formData.gender">
                                <option value="female">Female</option>
                                <option value="male">Male</option>
                                <option value="other">Other</option>
                            </select>
                            <div v-if="errors.gender" class="text-danger">{{ errors.gender }}</div>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label for="reason" class="form-label">Reason For Joining:</label><br>
                        <textarea id="reason" name="reason" rows="3" class="form-control"
                            @blur="() => validateReason(true)" @input="() => validateReason(false)"
                            v-model="formData.reason"></textarea><br>
                        <div v-if="errors.reason" class="text-danger">{{ errors.reason }}</div>
                    </div>
                    <div class="text-center">
                        <button type="sumbit" class="btn btn-primary me-2">Submit</button>
                        <button type="button" class="btn btn-secondary" @click="clearCards">Clear</button>
                    </div>
                </form>
                <div class="row mt-5" v-if="submittedCards.length">
                    <div class="d-flex flex-wrap justify-content-start">
                        <div v-for="(card, index) in submittedCards" :key="index" class="card m-2"
                            style="width: 18rem;">
                            <div class="card-header">
                                User Information
                            </div>
                            <ul class="list-group list-group-flush">
                                <li class="list-group-item">Username: {{ card.username }}</li>
                                <li class="list-group-item">Password: {{ card.password }}</li>
                                <li class="list-group-item">Australian Resident: {{ card.isAustralian ? 'Yes' : 'No' }}
                                </li>
                                <li class="list-group-item">Gender: {{ card.gender }}</li>
                                <li class="list-group-item">Reason: {{ card.reason }}</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const formData = ref({
    username: '',
    password: '',
    isAustralian: false,
    reason: '',
    gender: ''
});

const submittedCards = ref([]);



const submitForm = () => {
    validateName(true);
    validatePassword(true);
    validateResident(true);
    validateGender(true);
    validateReason(true);
    if (!errors.value.username &&
        !errors.value.password &&
        !errors.value.resident &&
        !errors.value.gender &&
        !errors.value.reason) {
        submittedCards.value.push({ ...formData.value });
        clearForm();
    }
};

const clearForm = () => {
    formData.value = {
        username: '',
        password: '',
        isAustralian: false,
        reason: '',
        gender: ''
    }
}

const clearCards = () => {
    submittedCards.value = ref([])
}

const errors = ref({
    password: null,
    username: null,
    resident: null,
    reason: null,
    gender: null
})

const validateName = (blur) => {
    if (formData.value.username.length < 3) {
        if (blur) errors.value.username = "Name must be at least 3 characters";
    }
    else {
        errors.value.username = null
    }
}

const validatePassword = (blur) => {
    const password = formData.value.password;
    const minLength = 8;
    const hasUppercase = /[A-Z]/.test(password);
    const hasLowercase = /[a-z]/.test(password);
    const hasNumber = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);

    if (password.length < minLength) {
        if (blur) errors.value.password = `Password must be at least ${minLength} characters long.`;
    } else if (!hasUppercase) {
        if (blur) errors.value.password = "Password must contain at least one uppercase letter.";
    } else if (!hasLowercase) {
        if (blur) errors.value.password = "Password must contain at least one lowercase letter.";
    } else if (!hasNumber) {
        if (blur) errors.value.password = "Password must contain at least one number.";
    } else if (!hasSpecialChar) {
        if (blur) errors.value.password = "Password must contain at least one special character.";
    } else {
        errors.value.password = null;
    }

}

const validateResident = (blur) => {
    if (formData.value.isAustralian == false) {
        if (blur) errors.value.resident = "Resident must be the Australia Resident";
    }
    else {
        errors.value.username = null
    }
}

const validateGender = (blur) => {
    if (formData.value.gender == '') {
        if (blur) errors.value.gender = "You must choose one choice in gender selector";
    }
    else {
        errors.value.username = null
    }
}

const validateReason = (blur) => {
    if (formData.value.reason.length < 10) {
        if (blur) errors.value.reason = "Reason must be at least 10 characters";
    }
    else if (formData.value.reason.length > 250) {
        if (blur) errors.value.reason = "Reason must be at most 250 characters";
    }
    else {
        errors.value.reason = null
    }
}

</script>

<style scoped>
.card {
    border: 1px solid #ccc;
    border-radius: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.card-header {
    background-color: #275FDA;
    color: white;
    padding: 10px;
    border-radius: 10px 10px 0 0;
}

.list-group-item {
    padding: 10px;
}
</style>