<script setup></script>

<template>
  <section class="card hourly-forecast">
    <div>Hourly forecast</div>

    <div class="cards-wrapper">
      <div class="card" v-for="value in 8">
        <div>
          <img src="/src/assets/images/icon-fog.webp" alt="" />
          <span>3 PM</span>
        </div>
        <div>20°</div>
      </div>
    </div>
  </section>
</template>
