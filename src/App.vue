<template>
  <div id="app">
    <div 
      class="header"
      v-bind:class="{ 'header-fullscreen': fullScreen }"
    >
      <div class="header-content"></div>
      <p>{{scrollNumber}}</p>
    </div>

    <div 
    class="map-viewport"
    >
        <div v-on:click.capture="controlsClick" class="controls"></div>
    </div>

    <Scroller
        v-on:scroll="scrollNumber = $event"
        v-on:fullscreen="fullScreen = $event"
    />
  </div>
</template>

<script>
import Scroller from './components/Scroller.vue'

export default {
  name: 'app',
  components: {
    Scroller
  },
  data: function () {
    return {
        scrollNumber: 0,
        fullScreen: false,
    }
  },
  methods: {
      controlsClick() {
        console.log('controls');
    },
  }
}
</script>

<style>

body {
    background: #2e2e2e;
}


#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2e2e2e;

    position: absolute;
    width: 100%;
    top: 0;
    bottom: 0;
    max-width: 420px;
    max-height: 830px;
    overflow: hidden;
}

@media screen and (min-width: 421px) {
    #app {
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
    }
}

/*
App reset by Ben Frain @benfrain / benfrain.com
Latest: https://github.com/benfrain/app-reset
An opinionated set of resets suitable for building web applications.
## Accessibility Notes
These resets target HTML elements that typically receive styling defaults by User Agents that I always need to 'undo'.
Be aware that some of these resets have a negative impact on the default usability and accessibility of a web page. Therefore, ensure you add an equivalent accessible style back that matches your project aesthetic.
## You'll want to run this through Autoprefixer You'll typically need to run this through (https://github.com/postcss/autoprefixer) for production. Only essential prefixes are added here (e.g. proprietary property value/pairs) and you'll need to set prefixing relative to your desired browser support matrix.
*/

/*Hat tip to @thierrykoblentz for this approach: https://css-tricks.com/inheriting-box-sizing-probably-slightly-better-best-practice/ */
html {
    box-sizing: border-box;
	/* Nicer looking fonts for OS X and iOS */
	-webkit-font-smoothing: antialiased; 
}

/*Yes, the universal selector. No, it isn't slow: https://benfrain.com/css-performance-revisited-selectors-bloat-expensive-styles/*/
* {
    /* Later browsers can be more easily reset with `all: unset`. It's commented out by default here as it's pretty heavy handed. However, further declarations on these elements follow for older browsers. If you uncomment this property/value REMEMBER accessibility for outlines etc. This undoes a LOT of default UA styling, use with EXTREME caution!! */
    /* all: unset; */
    /*This prevents users being able to select text. Stops long presses in iOS bringing up copy/paste UI for example. Note below we specifically switch user-select on for inputs for the sake of Safari. Bug here: https://bugs.webkit.org/show_bug.cgi?id=82692*/
    user-select: none;
    /*This gets -webkit specific prefix as it is a non W3C property*/
    -webkit-tap-highlight-color: rgba(255,255,255,0);
    /*Older Androids need this instead*/
    -webkit-tap-highlight-color: transparent;
    /* Most devs find border-box easier to reason about. However by inheriting we can mix box-sizing approaches.*/
    box-sizing: inherit;
}

*:before,
*:after {
    box-sizing: inherit;
}

/* Switching user-select on for inputs and contenteditable specifically for Safari (see bug link above)*/
input[type],
[contenteditable] {
	user-select: text;
}

body,
h1,
h2,
h3,
h4,
h5,
h6,
p {
    /*We will be adding our own margin to these elements as needed.*/
    margin: 0;
    /*You'll want to set font-size as needed.*/
    font-size: 1rem;
    /*No bold for h tags unless you want it*/
    font-weight: 400;
}

/* 
Thanks to L. David Baron for this:
https://twitter.com/davidbaron/status/794138427952222210 */
base, basefont, datalist, head, meta, script, style, title,
noembed, param, template {
    display: none;
}

a {
    text-decoration: none;
    color: inherit;
}

/*No bold for b tags by default*/
b {
    font-weight: 400;
}

/*Prevent these elements having italics by default*/
em,
i {
    font-style: normal;
}

/*IMPORTANT: This removes the focus outline for most browsers. Be aware this is a backwards accessibilty step! Mozilla (i.e. Firefox) also adds a dotted outline around a tags and buttons when they receive tab focus which I haven't found an unhacky way of removing.*/
a:focus,
button:focus {
    outline: 0;
}

/* The button element tends to get a lot of default styles which we largely undo here. We set text-alignment (usually set to center by UA style sheet) and the font-family to inherit from your own styles instead. */
button {
    appearance: none;
    background-color: transparent;
    border: 0;
    padding: 0;
    text-align: inherit;
    font-family: inherit;
}

input,
fieldset {
    appearance: none;
    border: 0;
    padding: 0;
    margin: 0;
    /*inputs and fieldset defaults to having a min-width equal to its content in Chrome and Firefox (https://code.google.com/p/chromium/issues/detail?id=560762), we may not want that*/
    min-width: 0;
    /*Reset the font size and family*/
    font-size: 1rem;
    font-family: inherit;
}

/* For IE, we want to remove the default cross ('X') that appears in input fields when a user starts typing - Make sure you add your own! */
input::-ms-clear {
    display: none;
}

/*This switches the default outline off when an input receives focus (really important for users tabbing through with a keyboard) so ensure you put something decent in for your input focus instead!!*/
input:focus {
    outline: 0;
}

input[type="number"] {
    /*Mozilla shows the spinner UI on number inputs unless we use this:*/
    -moz-appearance: textfield;
}

/*Removes the little spinner controls for number type inputs (WebKit browsers/forks only)*/
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
    appearance: none;
}

/*SVG defaults to display: inline which I dislike. Inline-block or inline-flex will render white space on SVG elements in HTML (where you would have defs and symbols) if the container isn't a flex box or the font-size set to 0 to crush the whitespace */
svg {
    display: block;
}

img {
    /*Make images behave responsively. Here they will scale up to 100% of their natural size*/
    max-width: 100%;
    /*Make images display as a block (UA default is usually inline)*/
    display: block;
}

/*Removes the default focusring that Mozilla places on select items. From: http://stackoverflow.com/a/18853002/1147859 
Ensure you set `#000` to the colour you want your text to appear */
select:-moz-focusring {
    color: transparent;
    text-shadow: 0 0 0 #000;
}

/* Switching on box-sizing: border-box by default; toggle this off if you want more granular control */
body {
	box-sizing: border-box;
}





  .header {
    height: 48px;
    padding: 0px 20px;
    position: absolute;
    width: 100%;
    z-index: 2;
    display: flex;
    align-items: center;
    justify-content: space-between;
    box-shadow: 0 2px 5px 0 rgba(74, 74, 74, 0.2);
    background-color: #fff;
    z-index: 3;
  }

  .header-fullscreen {
    box-shadow: 0 0px 1px 0px rgba(74, 74, 74, 0.2);
    border-radius: 0px;
  }

  .header-content {
    background-color: #ededed;
    height: 20px;
    width: 140px;
  }



  .map-viewport {
    position: absolute;
    top: 48px;
    height: 198px;
    left: 0;
    right: 0;
    pointer-events: none;
  }

  .marker {
    position: absolute;
    width: 15px;
    height: 30px;
    background-color: #9b9b9b;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }

  .controls {
    width: 30px;
    height: 60px;
    background-color: #9b9b9b;
    position: absolute;
    top: 20px;
    right: 20px;
    pointer-events: all;
    z-index: 1;
  }

</style>
