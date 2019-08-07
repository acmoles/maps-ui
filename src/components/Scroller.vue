<template>
  <div 
    ref="outer"
    class="outer"
    v-on:scroll.native="handleScrollOuter"
    >

    <div class="map">
      <div class="map-in-view">
        <div class="marker"></div>
      </div>
    </div>

    <div class="box elevation2 header">
      <div class="header-content"></div>
      <p>{{number}}</p>
    </div>

    <div 
      class="content-wrapper" 
      ref="inner"
      >

      <div 
        class="content box elevation2-reversed"
        v-bind:class="{ 'content-active': contentActive }"
      >
        <div class="content-toggle">
          <div class="content-toggle-icon"></div>
        </div>
        <div class="title"></div>
        <div class="icon"></div>
        <div class="content-group">
          <div v-for="n in 24" class="content-line">{{n}}</div>
        </div>
      </div>

    </div>
      
  </div>
</template>

<script>
export default {
  name: 'Scroller',
  props: {
    msg: String
  },
  data: function () {
    return {
      contentActive: true,
      number: 0
    }
  },
  mounted() {
    this.$refs.outer.addEventListener('scroll', this.handleScrollOuter);

  },
  methods: {
    handleScrollOuter() {

      this.number = this.$refs.outer.scrollTop;
      if (this.number >= 228) {
        // swap to full screen condition
        this.fullScreen = true;
      } else {
        // swap to normal condition
        this.fullScreen = false;
      }
    }
  }
}
</script>

<style>

  body {
    overflow: hidden;
  }

  .outer {
    overflow: scroll;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }

  .content-wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    pointer-events: none;
  }

  .content {
    position: relative;
    padding: 0 20px 20px 20px;
    height: 62%;
    pointer-events: all;
  }

  .content-active {
    /* top: calc(198px + 48px); */
    /* (on wrapper) animate top margin */
  }


  .map {
    background-color: #d7d7d7;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
  }

  .map-in-view {
    position: relative;
    top: 48px;
    height: 198px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .marker {
    width: 15px;
    height: 30px;
    background-color: #9b9b9b;
  }






  .box {
    background-color: #fff;
    border-radius: 0px 0px 3px 3px;
  }

  .elevation1 {
    box-shadow: 0 1px 3px 0 rgba(0,0,0,.15),0 0 2px 0 rgba(0,0,0,.15);
  }

  .elevation2 {
    box-shadow: 0 2px 5px 0 rgba(74, 74, 74, 0.2);
  }

  .elevation2-reversed {
    box-shadow: 0 2px 5px 0 rgba(74, 74, 74, 0.2);
    border-radius: 3px 3px 0px 0px;
    background-color: #fff;
  }

  .header {
    height: 48px;
    padding: 0px 20px;
    position: fixed;
    width: 100%;
    z-index: 2;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .content-line {
    height: 20px;
    background-color: #ededed;
    margin-bottom: 15px;
    color: #ededed;
  }

  .header-content {
    background-color: #ededed;
    height: 20px;
    width: 140px;
  }

  .title {
    height: 30px;
    background-color: #ededed;
    width: 200px;
    margin-bottom: 20px
  }

    .content-toggle {
    height: 30px;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
    margin-bottom: 20px;
  }

  .content-toggle-icon {
    width: 15px;
    height: 15px;
    background-color: #ededed;
  }

</style>
