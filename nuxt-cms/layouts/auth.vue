<template>
  <div class="hold-transition login-page">
    <div class="login-box">
      <Nuxt />
    </div>
  </div>
</template>

<script>
export default {
  middleware: "auth",
};
</script>

<style>
body {
  font-family: "Courier New", Courier, monospace;
}
.login-page {
  background: #ccdcec !important;
}

.card-info.card-outline {
  border-top: 3px solid #186399;
}

.btn-info {
  color: #fff;
  background-color: #186399;
  border-color: #186399;
  box-shadow: none;
}

.btn-info:hover {
  color: #fff;
  background-color: #12466b;
  border-color: #12466b;
}
</style>