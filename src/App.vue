<template>
  <template></template>
  
  <!--
    main block - screen
    left side bar and content
    footer
  -->
  <div class="screen">
    <div class="screen__wrapper">
      <div class="screen__content">
        <div class="left-sidebar"></div>
        <div class="inventory"></div>
      </div>
      <footer class="screen__footer footer">
        <div class="footer__block sceleton"></div>
        <div class="footer__cross-icon">
          <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M12 1.05L10.95 0L6 4.95L1.05 0L0 1.05L4.95 6L0 10.95L1.05 12L6 7.05L10.95 12L12 10.95L7.05 6L12 1.05Z" fill="white"/>
          </svg>
        </div>
      </footer>
    </div>
  </div>

</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'App',
  components: {
    // HelloWorld
  }
});
</script>

<style lang="scss">
@import './assets/scss/null-style.scss';

html {
  background: #1D1D1D;
}

#app {
 
}

.inventory{}.left-sidebar{}.screen{
  // full screen
  width: 100vw;
  height: 100vh;
  


  &__wrapper{
    padding: 32px;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
  }

  &__content{
    display: flex;
    flex-direction: row;
    flex-grow: 1;
  }&__footer{
    background: #262626;
    /* Primary Border */

    border: 1px solid #4D4D4D;
    border-radius: 12px;
    padding: 18px;

  }
  .footer{
    position: relative;

    &__block{
    border-radius: 12px;
    height: 50px;
    width: 90%;
  }&__cross-icon{
    padding: 8px;
    position: absolute;
    top: 8px;
    right: 8px;
  }}.sceleton{
    background: linear-gradient(90deg, #3C3C3C 0%, #444444 51.04%, #333333 100%);

    position: relative;
    overflow: hidden;
    &::after {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      transform: translateX(-100%);
      background-image: linear-gradient(
        90deg,
        rgba(#fff, 0) 0,
        rgba(#fff, 0.2) 20%,
        rgba(#fff, 0.5) 60%,
        rgba(#fff, 0)
      );
      animation: shimmer 3s infinite linear;
      content: '';
    }
    @keyframes shimmer {
      100% {
        transform: translateX(100%);
      }
    }
  }.screen{&__footer{}}


}



</style>
