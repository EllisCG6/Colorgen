<template>
  <div>
    <header>
      <h2>Color Generator</h2>
      <input
        v-on:keyup.enter="submit()"
        required
        placeholder="#f15025"
        type="text"
        v-model="color"
      />
      <button @click="submit()" class="submit">Submit</button>
    </header>
    <section class="principal" v-if="bool">
      <div
        @click="clip(index)"
        v-clipboard:copy="converter[index]"
        class="color-con"
        :style="{ 'background-color': princ[index] }"
        v-for="(element, index) in princ"
        :key="index"
      >
        <div class="scris">
          <p :style="{ color: colorator[index] }">{{ perShow[index] }} %</p>
          <p :style="{ color: colorator[index] }">{{ converter[index] }}</p>
          <p class="alert" v-if="boolClip[index] == true">
            COPIED TO CLIPBOARD!
          </p>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      color: "",
      keys: {
        a: 10,
        b: 11,
        c: 12,
        d: 13,
        e: 14,
        f: 15,
      },
      princ: [],
      hexC: [],
      converter: [],
      bool: false,
      boolClip: [],
      perShow: [],
      kL: 0,
      kD: 11,
      colorator: [],
    };
  },
  methods: {
    submit() {
      if(this.color.charAt(0) == "#"){
      this.converter = [];
      this.perShow = [];
      this.kL = 0;
      this.kD = 11;
      this.converter[10] = this.color;
      this.perShow[10] = 0;
      let rgb = [];
      rgb[0] = this.color.substr(1, 2);
      rgb[1] = this.color.substr(3, 2);
      rgb[2] = this.color.substr(5, 6);
      for (let i = 0; i < 3; i++) {
        if (isNaN(parseInt(rgb[i].charAt(0)))) {
          if (isNaN(parseInt(rgb[i].charAt(1)))) {
            rgb[i + 3] =
              16 * this.keys[`${rgb[i].charAt(0)}`] +
              this.keys[`${rgb[i].charAt(1)}`];
          } else {
            rgb[i + 3] =
              16 * this.keys[`${rgb[i].charAt(0)}`] +
              parseInt(rgb[i].charAt(1));
          }
        } else {
          if (isNaN(parseInt(rgb[i].charAt(1)))) {
            rgb[i + 3] =
              16 * parseInt(rgb[i].charAt(0)) +
              this.keys[`${rgb[i].charAt(1)}`];
          } else {
            rgb[i + 3] =
              16 * parseInt(rgb[i].charAt(0)) + parseInt(rgb[i].charAt(1));
          }
        }
      }
      this.dark(rgb);
      this.light(rgb);
      this.princ[10] = "rgb(" + rgb[3] + "," + rgb[4] + "," + rgb[5] + ")";
      this.bool = true;
    }
    },
    light(rgb) {
      let per = 100;

      for (let i = 0; per >= 10; i++) {
        let rr = Math.round(rgb[3] + (per / 100) * (255 - rgb[3]));
        let gg = Math.round(rgb[4] + (per / 100) * (255 - rgb[4]));
        let bb = Math.round(rgb[5] + (per / 100) * (255 - rgb[5]));
        this.princ[i] = "rgb(" + rr + "," + gg + "," + bb + ")";
        this.perShow[this.kL] = per;
        this.rgbToHex(rr, gg, bb, this.kL);
        this.kL++;
        per -= 10;
      }
    },
    dark(rgb) {
      let per = 10;

      for (let i = 11; per <= 100; i++) {
        let rr = Math.round(rgb[3] - (per / 100) * rgb[3]);
        let gg = Math.round(rgb[4] - (per / 100) * rgb[4]);
        let bb = Math.round(rgb[5] - (per / 100) * rgb[5]);
        this.princ[i] = "rgb(" + rr + "," + gg + "," + bb + ")";
        this.perShow[this.kD] = per;
        this.rgbToHex(rr, gg, bb, this.kD);
        this.kD++;
        per += 10;
      }
      /* return this.princ; */
    },
    rgbToHex(rr, gg, bb, k) {
      var clrs = [rr, gg, bb];

      for (let i = 0; i < clrs.length; i++) {
        let cat = Math.trunc(clrs[i] / 16);
        let rest = clrs[i] % 16;
        if (cat < 10) {
          if (rest < 10) {
            this.hexC[i] = cat.toString() + rest.toString();
          } else {
            for (const letter in this.keys) {
              if (this.keys[letter] == rest) {
                this.hexC[i] = cat.toString() + letter;
              }
            }
          }
        } else {
          if (rest < 10) {
            for (const letter in this.keys) {
              if (this.keys[letter] == cat) {
                this.hexC[i] = letter + rest.toString();
              }
            }
          } else {
            for (const letter in this.keys) {
              if (this.keys[letter] == cat) {
                this.hexC[i] = letter;
              }
            }
            for (const letter in this.keys) {
              if (this.keys[letter] == rest) {
                this.hexC[i] += letter;
              }
            }
          }
        }
      }

      this.converter[k] = "#" + this.hexC[0] + this.hexC[1] + this.hexC[2];
    },
    clip(index) {
      this.boolClip[index] = true;
      this.$forceUpdate();
      this.timer(index);
    },
    timer(index) {
      clearTimeout(seconds);
      const seconds = setTimeout(() => {
        this.boolClip[index] = false;
        this.$forceUpdate();
      }, 3000);
    },
  },
  mounted() {
    let first = new Array(11);
    first.fill("#102A42", 0, 11);
    let second = new Array(10);
    second.fill("#ffffff", 0, 10);
    this.colorator = first.concat(second);
    this.color = "#f15025";
    this.boolClip = new Array(21).fill(false);
    this.submit();
  },
};
</script>

<style>
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  background: #f1f5f8;
}
.color-con {
  min-height: 156px;
  display: flex;
  min-width: 200px;
  flex-direction: column;
  cursor: pointer;
}
.principal {
  margin-top: 30px;
  min-height: calc(100vh - 100px);
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(223.33px, 1fr));
  grid-template-rows: repeat(auto-fit, minmax(96px, 1fr));
}
.alert {
  margin-top: -10px;
  color: #617d98;
}
.scris {
  padding: 1rem 2rem;
  font-size: 1rem;
}
input {
  border-color: transparent;
  border-top-left-radius: 0.25rem;
  border-bottom-left-radius: 0.25rem;
}
.submit {
  background: #49a6e9;
  border-color: transparent;
  border-top-right-radius: 0.25rem;
  border-bottom-right-radius: 0.25rem;
  text-transform: capitalize;
  color: #fff;
  cursor: pointer;
}
.header {
  text-align: center;
  display: flex;
  align-items: center;
  height: 100px;
  padding-left: 2rem;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
