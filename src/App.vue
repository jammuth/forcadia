<template>
  <div class="app">
    <nav>
    
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/about">About</RouterLink>
    </nav>

    <RouterView />
  </div>
</template>

<style scoped>
@import "@/assets/main.css";

nav {
  width: 100%;
  font-size: 20px;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}
</style>
