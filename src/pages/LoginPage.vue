<template>
  <div class="login-page" :style="backgroundStyle">
    <div class="login-page__overlay">
      <LoginForm />
      <p class="login-page__tagline">Digimon &gt; Pokémon</p>
    </div>
  </div>
</template>

<script>
import LoginForm from "src/components/auth/LoginFrom.vue";
import digimonBackground from "src/assets/digimon mayor que pokemon.jpeg";

export default {
  name: "LoginPage",
  components: {
    LoginForm
  },
  computed: {
    backgroundStyle() {
      return {
        backgroundImage: `linear-gradient(rgba(5, 0, 20, 0.7), rgba(5, 0, 20, 0.7)), url(${digimonBackground})`
      }
    }
  }
}
</script>

<style scoped>
.login-page {
  min-height: 100vh;
  width: 100%;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem 1rem;
  position: relative;
}

.login-page__overlay {
  width: 100%;
  max-width: 480px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.login-page__tagline {
  color: #f8fafc;
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
  margin: 0;
  text-shadow: 0 2px 12px rgba(0, 0, 0, 0.6);
}

@media (max-width: 450px) {
  .login-page__tagline {
    font-size: 0.85rem;
    text-align: center;
  }
}
</style>
