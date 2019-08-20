<template>
  <div 
    ref="outer"
    class="outer"
    v-on:scroll.native="handleScrollOuter"
    v-bind:style="{ 'scroll-behavior': initialised ? 'smooth' : 'auto' }"
    >

    <Map 
      v-bind:offset="screenOffset"
      v-bind:actions="actionsHeight"
      v-bind:width="width"
      v-bind:height="height"
      v-bind:contentActive="contentActive"
    />

    <div
      class="actions-spacer spacer"
      v-bind:class="{ snap: initialised }"
      v-bind:style="{ height: actionsHeight + 'px', top: height + 'px' }"
    ></div>

    <div
      class="map-port-spacer spacer"
      v-bind:style="{ height: screenOffset + 'px', top: (height + actionsHeight) + 'px' }"
    ></div>

    <div 
      class="content"
      v-bind:style="{ top: screenOffset + actionsHeight + 'px' }"
      ref="content"
    >
      
      <div class="content-header">
        <div 
          v-on:click="toggleContentActive"
          class="content-toggle elevation2-reversed"
          >
          <div 
            class="content-toggle-icon"
            v-bind:style="{ transform: contentActive ? 'rotate(0deg)' : 'rotate(180deg)' }"
          ></div>
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
</template>

<script>
import Map from './Map.vue'

export default {
  name: 'Scroller',
  components: {
    Map
  },
  data: function () {
    return {
      width: 0,
      height: 0,
      initialised: false,
      contentActive: true,
      fullScreen: false,
      screenOffset: 0,
      actionsHeight: 0,
      scrollNumber: 0,
    }
  },
  mounted() {
    this.width = this.$refs.outer.offsetWidth;
    this.height = this.$refs.outer.offsetHeight;

    console.log('width: ', this.width);
    console.log('height: ', this.height);

    this.$refs.outer.addEventListener('scroll', this.handleScrollOuter);
    this.getOffset();
    let scrollTo = (this.screenOffset + this.actionsHeight) - (198 + 48);
    console.log('scroll to: ', scrollTo);
    this.$refs.outer.scrollTo( 0 , scrollTo );
    this.initialised = true;
  },
  methods: {
    handleScrollOuter() {

      this.scrollNumber = this.$refs.outer.scrollTop;

      this.$emit('scroll', this.scrollNumber);

      if (this.scrollNumber >= this.actionsHeight + this.screenOffset - 48) {
        // swap to full screen condition
        this.fullScreen = true;
        this.$emit('fullscreen', this.fullScreen);
      } else {
        // swap to normal condition
        this.fullScreen = false;
        this.$emit('fullscreen', this.fullScreen);
      }

      if (this.scrollNumber == 0) {
        this.contentActive = false;
      } 
      // else if (this.scrollNumber > this.screenOffset) {
      //   this.contentActive = true;
      // }
    },
    getOffset() {
        let actionsRect = this.$refs.actions.getBoundingClientRect();

        // Update to relative to parent
        let parentRect = this.$refs.outer.getBoundingClientRect();
        let relativeBottom = actionsRect.bottom - parentRect.top;
        let relativeTop = actionsRect.top - parentRect.top;

        let heightActions = relativeBottom - relativeTop;
        let distanceToElementBottom = this.height - relativeBottom;

        console.log('actions height: ', heightActions);
        console.log('offset: ', distanceToElementBottom);

        this.screenOffset = distanceToElementBottom;
        this.actionsHeight = heightActions;

        // TODO re-get offset on window height change
    },
    toggleContentActive() {
      if (this.contentActive) {
        console.log('scroll to: ', 0);
        this.$refs.outer.scrollTo( 0 , 0 );
        this.contentActive = false;
      } else {
        let scrollTo = (this.screenOffset + this.actionsHeight) - (198 + 48);
        console.log('scroll to: ', scrollTo);
        this.$refs.outer.scrollTo( 0 , scrollTo );
        this.contentActive = true;
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
    scroll-snap-type: y proximity;
    -webkit-overflow-scrolling: touch;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }

  .fixed-outer {
    overflow: hidden;
  }

  .map {
    scroll-snap-align: end;
  }

  .spacer {
    pointer-events: none;
    position: absolute;
    width: 100%;
  }

  .snap {
    scroll-snap-align: end;
  }

  .content {
    position: absolute;
    width: 100%;
    pointer-events: all;
    transition: transform 0.3s ease;
    background-color: #fff;
    z-index: 2;
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

  /* .content-toggle-icon {
      width: 0; 
      height: 0; 
      border-left: 15px solid transparent;
      border-right: 15px solid transparent;
      border-top: 15px solid #ededed;
      transition: transform 0.3s ease;
  } */

    .content-toggle-icon {
      width: 30px; 
      height: 5px;
      background-color: #ededed;
      border-radius: 2.5px/50%;
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

  .content-line {
    height: 20px;
    background-color: #ededed;
    margin-bottom: 15px;
    color: #ededed;
  }

  .title {
    height: 30px;
    background-color: #ededed;
    width: 200px;
    margin-bottom: 20px
  }

</style>
