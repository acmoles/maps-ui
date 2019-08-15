<template>
  <div 
    ref="outer"
    class="outer"
    v-on:scroll.native="handleScrollOuter"
    v-bind:class="{ 'fixed-outer': contentInactive }"
    >

    <Map 
      v-bind:offset="screenOffset"
    />

    <div 
      class="header"
      v-bind:class="{ 'header-fullscreen': fullScreen }"
    >
      <div class="header-content"></div>
      <p>{{number}}</p>
    </div>

    <div 
      class="content-wrapper" 
      >

      <div 
        class="content"
        v-bind:class="{ 'content-inactive': contentInactive }"
        v-bind:style="{ transform: 'translateY(' + screenOffset + 'px' }"
        ref="content"
      >
        
        <div class="content-header">
          <div 
            v-on:click="toggleContentActive"
            class="content-toggle elevation2-reversed"
            >
            <div class="content-toggle-icon"></div>
          </div>

          <div 
            class="content-actions content-inner"
            ref="actions"
          >
            <div class="action"></div>
             <div class="action"></div>
          </div>
        </div>

        <div class="content-inner">
          <div class="title"></div>
          <div class="icon"></div>
          <div class="content-group">
          <div v-for="n in 24" class="content-line">{{n}}</div>
        </div>
        </div>

      </div>

    </div>
      
  </div>
</template>

<script>
import Map from './Map.vue'

export default {
  name: 'Scroller',
  components: {
    Map
  },
  props: {
    msg: String
  },
  data: function () {
    return {
      contentInactive: false,
      fullScreen: false,
      screenOffset: 0,
      number: 0
    }
  },
  mounted() {
    this.$refs.outer.addEventListener('scroll', this.handleScrollOuter);

  },
  methods: {
    handleScrollOuter() {

      this.number = this.$refs.outer.scrollTop;
      if (this.number >= 198) {
        // swap to full screen condition
        this.fullScreen = true;
      } else {
        // swap to normal condition
        this.fullScreen = false;
      }
    },
    toggleContentActive() {

      if (this.contentInactive === false) {
        //add translate attribute
      
        if (this.$refs.outer.scrollTop > 0) {
          //animate scroll area to zero
          this.$refs.outer.scrollTo( 0 , 0 );
        }

        let distanceToElementBottom = window.innerHeight - this.$refs.actions.getBoundingClientRect().bottom;

        console.log(distanceToElementBottom);

        this.screenOffset = distanceToElementBottom;

        this.contentInactive = true;

      } else {
        //remove translate attribute

        this.screenOffset = 0;

        this.contentInactive = false;
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

  .fixed-outer {
    overflow: hidden;
  }

  .content-wrapper {
    pointer-events: none;
    position: relative;
  }

  .content {
    position: absolute;
    top: calc(198px + 48px);
    width: 100%;
    pointer-events: all;
    transition: transform 0.3s ease;
    background-color: #fff;
  }

  .content-inner {
    padding: 20px;
  }

  /* .content-inactive {
    transform: translateY(100%);
  } */




  .content-actions {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #ededed;
  }

  .action {
    width: 100px;
    height: 30px;
    background-color: #ededed;
  }

  .action:first-child {
    width: 160px;
  }

  .content-toggle {
    height: 30px;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
  }

  .content-toggle-icon {
    width: 15px;
    height: 15px;
    background-color: #ededed;
  }






  .elevation1 {
    box-shadow: 0 1px 3px 0 rgba(0,0,0,.15),0 0 2px 0 rgba(0,0,0,.15);
    background-color: #fff;
    border-radius: 0px 0px 3px 3px;
  }

  .elevation2 {
    box-shadow: 0 2px 5px 0 rgba(74, 74, 74, 0.2);
    background-color: #fff;
    border-radius: 0px 0px 3px 3px;
  }

  .elevation2-reversed {
    box-shadow: 0 -1px 2px 0 rgba(74, 74, 74, 0.1);
    border-radius: 6px 6px 0px 0px;
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
    box-shadow: 0 2px 5px 0 rgba(74, 74, 74, 0.2);
    background-color: #fff;
  }

  .header-fullscreen {
    box-shadow: 0 0px 1px 0px rgba(74, 74, 74, 0.2);
    border-radius: 0px;
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

</style>
