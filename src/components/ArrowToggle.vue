<template>
          <div>
              <svg height="30" viewBox="0 0 40 30" width="40" xmlns="http://www.w3.org/2000/svg">
              <path 
                v-bind:d="'m5 ' + startY + ' 15 ' + centreY + ' 15 ' + endY" 
                fill="none" 
                stroke="#d7d7d7" 
                stroke-linecap="round" 
                stroke-linejoin="round" 
                stroke-width="3"
              />
              </svg>
          </div>
</template>

<script>
// const downPositions = {
//     startYdown: 12,
//     centreYdown: 6,
//     endYdown: -6
// }

// const upPositions = {
//     startYup: 18,
//     centreYup: -6,
//     endYup: 6
// }
const startYdown = 12;
const centreYdown = 6;
const endYdown = -6;

const startYup = 18;
const centreYup = -6;
const endYup = 6;

const duration = 1500;

export default {    
  name: 'ArrowToggle',
  props: {
    down: Boolean,
  },
  data: function () {
    return {
        startY: 14,
        centreY: 0,
        endY: 0,
        startTime: null
    }
  },
  watch: {
    down: function (boolean) {    
        this.toggle(boolean);
    },
  },
  mounted: function() {
      this.toggle(this.down);
  },
  methods: {
    toggle(boolean) {
        if (boolean) {
            // down true, content active
            this.startAnimation(startYdown, centreYdown, endYdown);
            console.log('inactive - active');
        } else {
            // down false (up), content inactive
            this.startAnimation(startYup, centreYup, endYup);
            console.log('active - inactive');
        }
    },
    startAnimation(targetStartY, targetCentreY, targetEndY) {
        this.startTime = performance.now();
        window.requestAnimationFrame(
            () => { this.update(targetStartY, targetCentreY, targetEndY) }
        );
    },
    update(targetStartY, targetCentreY, targetEndY) {

        let timeSinceStart = (performance.now() - this.startTime);

        let l = Math.min(timeSinceStart / duration, 1);

        // it lerps the lerped values - but looks ok :)
        this.startY = this.lerp(this.startY, targetStartY, l);
        this.centreY = this.lerp(this.centreY, targetCentreY, l);
        this.endY = this.lerp(this.endY, targetEndY, l);

        if (l < 0.95) {
            window.requestAnimationFrame(
                () => { this.update(targetStartY, targetCentreY, targetEndY) }
            );
        }
    },
    lerp(v0, v1, t) {
        // cosine easing
        let t2 = (1-Math.cos(t*Math.PI))/2;
        return(v0*(1-t2)+v1*t2);
    },
  }
  }

</script>

<style>

</style>