<template>
    <div class="login-container">
        <div class="form-title">
            <h2>Login to Your Account</h2>
        </div>
        <a-form :form="loginForm" @submit.prevent="onSubmit" class="login-form">
            <a-form-item name="email" rules="[ { required: true, message: 'Please input your email!' } ]">
                <a-input v-model:value="email" placeholder="Email" />
            </a-form-item>

            <a-form-item name="password" rules="[ { required: true, message: 'Please input your password!' } ]">
                <a-input-password v-model:value="password" placeholder="Password" />
            </a-form-item>

            <a-form-item>
                <a-button type="primary" htmlType="submit" class="login-form-button" :loading="loading">
                    Log in
                </a-button>
            </a-form-item>
        </a-form>

        <div class="signup-link">
            <p>Don't have an account? <a @click="goToSignup" class="signup-link-text">Create an Account</a></p>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { message } from 'ant-design-vue';
import { useRouter } from 'vue-router';

export default defineComponent({
    name: "LogIn",
    setup() {
        const router = useRouter();
        const loginForm = ref(null);
        const email = ref('');
        const password = ref('');
        const loading = ref(false);

        const onSubmit = async () => {
            console.log('Email:', email.value);
            console.log('Password:', password.value);

            if (!email.value || !password.value) {
                message.error('Please input your email and password!');
                return;
            }

            loading.value = true;

            try {
                const response = await fetch('http://localhost:3000/auth/login', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        email: email.value,
                        password: password.value,
                    }),
                });

                const data = await response.json();

                if (response.status === 200) {
                    message.success('Login successful!');

                    localStorage.setItem('nerd-dev', JSON.stringify({ isAuthenticated: true }));

                    router.push('/');
                } else if (response.status === 404) {
                    message.error('User not found');
                } else if (response.status === 400 && data.message === 'Invalid credentials') {
                    message.error('Invalid credentials');
                } else {
                    message.error('Login failed. Please try again.');
                }
            } catch (error) {
                console.error('Error during login:', error);
                message.error('An error occurred during login. Please try again.');
            } finally {
                loading.value = false;
            }
        };

        const goToSignup = () => {
            router.push('/signup');
        };

        return {
            loginForm,
            email,
            password,
            loading,
            onSubmit,
            goToSignup,
        };
    },
});
</script>

<style scoped>
.login-container {
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

.login-form {
    width: 300px;
    padding: 40px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.login-form-button {
    width: 100%;
}

.signup-link {
    margin-top: 20px;
    text-align: center;
}

.signup-link-text {
    color: #1890ff;
    cursor: pointer;
    text-decoration: underline;
}
</style>
