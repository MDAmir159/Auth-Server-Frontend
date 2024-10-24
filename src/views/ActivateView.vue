<template>
    <div class="activation-container">
        <h2>Activating your account...</h2>
        <p v-if="activationMessage">{{ activationMessage }}</p>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { message } from 'ant-design-vue';

export default defineComponent({
    name: "ActivateView",
    setup() {
        const route = useRoute();
        const router = useRouter();
        const activationMessage = ref('');

        onMounted(async () => {
            const token = route.params.token;
            try {
                const response = await fetch(`http://localhost:3000/auth/activate/${token}`, {
                    method: 'GET',
                });

                const data = await response.json();

                if (response.status === 200) {
                    activationMessage.value = 'Your account has been activated! You can now login. Redirecting you to login page in 5 seconds, standby.';
                    message.success('Account activated!');
                    setTimeout(() => {
                        router.push('/login');
                    }, 5000);
                } else {
                    activationMessage.value = data.message || 'Account activation failed.';
                    message.error('Account activation failed.');
                }
            } catch (error) {
                console.log(error);
                activationMessage.value = `An error occurred during activation. Please try again.`;
                // activationMessage.value = `${error}`
                message.error(`An error occurred during activation.`);
                // message.error(`${error}`);
            }
        });

        return {
            activationMessage,
        };
    },
});
</script>

<style scoped>
.activation-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
}
</style>
