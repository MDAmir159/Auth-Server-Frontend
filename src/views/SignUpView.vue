<template>
    <div class="signup-container">
        <div class="form-title">
            <h2>Create Your Account</h2>
        </div>
        <a-form :form="signupForm" @submit.prevent="onSubmit" class="signup-form">
            <a-form-item name="firstName" rules="[ { required: true, message: 'Please input your first name!' } ]">
                <a-input v-model:value="firstName" placeholder="First Name" />
            </a-form-item>

            <a-form-item name="lastName" rules="[ { required: true, message: 'Please input your last name!' } ]">
                <a-input v-model:value="lastName" placeholder="Last Name" />
            </a-form-item>

            <a-form-item name="email" rules="[ { required: true, type: 'email', message: 'Please input a valid email!' } ]">
                <a-input v-model:value="email" placeholder="Email" />
            </a-form-item>

            <a-form-item name="mobileNumber" rules="[ { required: true, message: 'Please input your mobile number!' } ]">
                <a-input v-model:value="mobileNumber" placeholder="Mobile Number" />
            </a-form-item>

            <a-form-item name="password" rules="[ { required: true, message: 'Please input your password!' } ]">
                <a-input-password v-model:value="password" placeholder="Password" />
            </a-form-item>

            <a-form-item name="confirmPassword" rules="[ { required: true, message: 'Please confirm your password!' } ]">
                <a-input-password v-model:value="confirmPassword" placeholder="Confirm Password" />
            </a-form-item>

            <a-form-item>
                <a-button type="primary" htmlType="submit" class="signup-form-button" :loading="loading">
                    Sign Up
                </a-button>
            </a-form-item>
        </a-form>
        
        <div v-if="activationLink" class="activation-link">
            <p>Signup successful! <a @click="goToActivationPage" class="activation-link-text">Click here</a> to activate your account.</p>
        </div>

        <div class="login-link">
            <p>Already have an account? <a @click="goToLogin" class="login-link-text">Log in</a></p>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { message } from 'ant-design-vue';
import { useRouter } from 'vue-router';

export default defineComponent({
    name: "SignUp",
    setup() {
        const router = useRouter();
        const signupForm = ref(null);
        const firstName = ref('');
        const lastName = ref('');
        const email = ref('');
        const mobileNumber = ref('');
        const password = ref('');
        const confirmPassword = ref('');
        const loading = ref(false);
        const activationLink = ref('');


        const onSubmit = async () => {
            console.log('First Name:', firstName.value);
            console.log('Last Name:', lastName.value);
            console.log('Email:', email.value);
            console.log('Mobile Number:', mobileNumber.value);
            console.log('Password:', password.value);
            console.log('Confirm Password:', confirmPassword.value);

            if (!firstName.value || !lastName.value || !email.value || !mobileNumber.value || !password.value || !confirmPassword.value) {
                message.error('Please fill in all the fields.');
                return;
            }

            if (password.value !== confirmPassword.value) {
                message.error('Passwords do not match!');
                return;
            }

            loading.value = true;

            try {
                const response = await fetch('http://localhost:3000/auth/signup', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        firstName: firstName.value,
                        lastName: lastName.value,
                        email: email.value,
                        mobileNumber: mobileNumber.value,
                        password: password.value,
                    }),
                });

                const data = await response.json();

                if (response.status === 200) {
                    message.success('Signup successful! Now activate your account.');
                    activationLink.value = data.activationLink;
                } else if (response.status === 400 && data.message === 'Email already exists') {
                    message.error('Email already exists. Please use a different email.');
                } else if (response.status === 400 && data.message === 'Mobile number already exists') {
                    message.error('Mobile number already exists. Please use a different number.');
                } else {
                    message.error('Signup failed. Please try again.');
                }
            } catch (error) {
                console.error('Error during signup:', error);
                message.error('An error occurred during signup. Please try again.');
            } finally {
                loading.value = false;
            }
        };

         const goToActivationPage = () => {
            if (activationLink.value) {
                const token = activationLink.value.split('/').pop()
                router.push(`/activate/${token}`);
            }
        };

        const goToLogin = () => {
            router.push('/login');
        };

        return {
            signupForm,
            firstName,
            lastName,
            email,
            mobileNumber,
            password,
            confirmPassword,
            loading,
            activationLink,
            onSubmit,
            goToActivationPage,
            goToLogin
        };
    },
});
</script>

<style scoped>
.signup-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f0f2f5;
}

.form-title {
    text-align: center;
    margin-bottom: 20px;
}

.signup-form {
    width: 300px;
    padding: 40px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.signup-form-button {
    width: 100%;
}

.activation-link {
    margin-top: 20px;
    text-align: center;
}

.activation-link-text {
    color: #1890ff;
    cursor: pointer;
    text-decoration: underline;
}

.login-link {
    margin-top: 20px;
    text-align: center;
}

.login-link-text {
    color: #1890ff;
    cursor: pointer;
    text-decoration: underline;
}
</style>
