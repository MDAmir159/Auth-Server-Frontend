<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

const isAuthenticated = ref(false);

const checkAuthStatus = () => {
  const authData = JSON.parse(localStorage.getItem('nerd-dev') || '{}');
  if (authData.isAuthenticated) {
    isAuthenticated.value = true;
  } else {
    isAuthenticated.value = false;
  }
};

checkAuthStatus();

const goToLogin = () => {
  router.push('/login');
};

const goToSignup = () => {
  router.push('/signup');
};

const logout = () => {
  localStorage.removeItem('nerd-dev');
  isAuthenticated.value = false;
  router.push('/');
};
</script>

<template>
  <div style="text-align: center; margin-top: 50px;">
    <h1>Welcome to Our Platform</h1>
    <p v-if="!isAuthenticated">Get started by logging in or signing up!</p>
    <div v-if="!isAuthenticated">
      <button @click="goToLogin" style="margin-right: 10px;">Login</button>
      <button @click="goToSignup">Signup</button>
    </div>
    <div v-else>
      <p>You are logged in!</p>
      <button @click="logout" style="background-color: red;">Logout</button>
    </div>
  </div>
</template>

<style scoped>
button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 5px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #0056b3;
}

button[style="background-color: red;"] {
  background-color: #dc3545;
}

button[style="background-color: red;"]:hover {
  background-color: #c82333;
}
</style>
