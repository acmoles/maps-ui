<template>
  <div 
    ref="outer"
    class="outer"
    v-on:scroll.native="handleScrollOuter"
    v-bind:style="{ 'scroll-behavior': initialised ? 'smooth' : 'auto' }"
    >

    <Map 
      ref="map"
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
      class="content"
      v-bind:style="{ top: screenOffset + actionsHeight + 'px' }"
      ref="content"
    >
      
        <div 
          v-on:click="toggleContentActive"
          class="content-toggle elevation2-reversed"
          >
        <ArrowToggle
          v-bind:down="contentActive"
        />
        </div>

        <div 
          class="content-actions"
          ref="actions"
        >
          <div class="directions-title">
            <div></div>
            <div></div>
            <div></div>
          </div>
          <div class="directions-action"></div>
        </div>

      <div class="content-inner">
        <div class="content-group">
        <div v-for="n in 6" class="content-line">
          <div></div>
          <div>{{n}}</div>
          <div v-bind:style="{ height: (2 % n)*50 + 'px', display: 4 % n === 0 ? 'none' : 'flex' }"></div>
        </div>
      </div>
      </div>
      <div class="content-footer"></div>

    </div>
      
  </div>
</template>

<script>
import Map from './Map.vue'
import ArrowToggle from './ArrowToggle.vue'

export default {
  name: 'Scroller',
  components: {
    Map,
    ArrowToggle
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
      activeScrollPosition: null,
    }
  },
  mounted() {
    this.width = this.$refs.outer.offsetWidth;
    this.height = this.$refs.outer.offsetHeight;

    console.log('width: ', this.width);
    console.log('height: ', this.height);

    this.getOffset();
    this.activeScrollPosition = (this.screenOffset + this.actionsHeight) - (198 + 48);

    this.$nextTick(() => {
    // Operate after dom updates with dynamic values - since scroll position depends on it
    this.$refs.outer.addEventListener('scroll', this.handleScrollOuter);

    console.log('scroll to: ', this.activeScrollPosition);
    this.$refs.outer.scrollTo( 0 , this.activeScrollPosition );

    // Finish initialisation
    this.$refs.map.moveToActiveInitial(this.width, this.height, this.screenOffset, this.actionsHeight);
    this.initialised = true;

    });
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
      // This feels jarring actually
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
        console.log('scroll to: ', this.activeScrollPosition);
        this.$refs.outer.scrollTo( 0 , this.activeScrollPosition );
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




  .content-actions {
    padding: 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #ededed;
  }

  .directions-title {
    flex-grow: 1;
  }

  .directions-action {
    flex-grow: 0;
    height: 70px;
    width: 70px;
    align-self: flex-end;
    background-color: #ededed;
    margin-left: 40px;
  }

  .directions-title div {
    height: 16px;
    background-color: #ededed;
  }

  .directions-title div:not(:last-child) {
      margin-bottom: 5px
  }

  .directions-title div:last-child {
      width: 80%;
  }

  .directions-title div:first-child {
    height: 36px;
    background-color: #ededed;
    width: 200px;
    margin-bottom: 10px
  }

  .content-toggle {
    height: 30px;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
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

  .content-inner {
    padding: 20px;
    padding-top: 10px;
  }

  .content-line {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    padding-bottom: 20px;
  }

  .content-line div {
    flex-grow: 1;
    height: 15px;
    color: #ededed;
    background-color: #ededed;
    margin-top: 20px;
  }

  .content-line div:first-child {
    flex-grow:0;
    width: 15px;
    margin-right: 15px;
  }

  .content-line div:last-child {
    width: 100%;
    margin-left: 30px;
  }

  .content-footer {
    width: 100%;
    height: 45px;
    background-color: #ededed;
  }

</style>
