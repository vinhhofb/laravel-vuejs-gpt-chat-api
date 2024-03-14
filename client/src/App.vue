<template>
  <div class="container">
    <h2 class="title">Password Generator</h2>
    <div class="result">
      <div class="result__title field-title">Generated Password</div>
      <div class="result__info right">click to copy</div>
      <div class="result__info left">copied</div>
      <div class="result__viewbox" id="result">{{ generatedPassword }}</div>
    </div>
    <div class="length range__slider" data-min="4" data-max="32">
      <div class="length__title field-title" :data-length="length">{{ length }}</div>
      <input id="slider" type="range" :min="minLength" :max="maxLength" v-model="length" />
    </div>
    <div class="settings">
      <span class="settings__title field-title">settings</span>
      <div class="setting">
        <input type="checkbox" id="uppercase" v-model="includeUppercase" ref="uppercase" />
        <label for="uppercase">Include Uppercase</label>
      </div>
      <div class="setting">
        <input type="checkbox" id="lowercase" v-model="includeLowercase" ref="lowercase" />
        <label for="lowercase">Include Lowercase</label>
      </div>
      <div class="setting">
        <input type="checkbox" id="number" v-model="includeNumbers" ref="number" />
        <label for="number">Include Numbers</label>
      </div>
      <div class="setting">
        <input type="checkbox" id="symbol" v-model="includeSymbols" ref="symbol" />
        <label for="symbol">Include Symbols</label>
      </div>
    </div>
    <button class="btn generate" @click="generatePassword">Generate Password</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      length: 16,
      minLength: 4,
      maxLength: 32,
      includeUppercase: true,
      includeLowercase: true,
      includeNumbers: true,
      includeSymbols: false,
      generatedPassword: ''
    };
  },
  methods: {
    generatePassword() {
      const length = parseInt(this.length);
      const hasLower = this.includeLowercase;
      const hasUpper = this.includeUppercase;
      const hasNumber = this.includeNumbers;
      const hasSymbol = this.includeSymbols;

      this.generatedPassword = this._generatePassword(
        length,
        hasLower,
        hasUpper,
        hasNumber,
        hasSymbol
      );
    },
    _generatePassword(length, lower, upper, number, symbol) {
      let generatedPassword = "";
      const typesCount = lower + upper + number + symbol;
      const typesArr = [
        { lower },
        { upper },
        { number },
        { symbol }
      ].filter(item => Object.values(item)[0]);

      if (typesCount === 0) {
        return "";
      }

      for (let i = 0; i < length; i++) {
        typesArr.forEach(type => {
          const funcName = Object.keys(type)[0];
          generatedPassword += this._randomFunc[funcName]();
        });
      }

      return generatedPassword
        .slice(0, length)
        .split("")
        .sort(() => Math.random() - 0.5)
        .join("");
    },
    _randomFunc: {
      lower: function() {
        return String.fromCharCode(Math.floor(Math.random() * 26) + 97);
      },
      upper: function() {
        return String.fromCharCode(Math.floor(Math.random() * 26) + 65);
      },
      number: function() {
        return String.fromCharCode(Math.floor(Math.random() * 10) + 48);
      },
      symbol: function() {
        const symbols = "~!@#$%^&*()_+{}\":?><;.,";
        return symbols[Math.floor(Math.random() * symbols.length)];
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      const checkboxes = [
        this.$refs.uppercase,
        this.$refs.lowercase,
        this.$refs.number,
        this.$refs.symbol
      ];

      checkboxes.forEach(checkbox => {
        checkbox.addEventListener("click", this._disableOnlyCheckbox);
      });
    });
  },
  beforeDestroy() {
    const checkboxes = [
      this.$refs.uppercase,
      this.$refs.lowercase,
      this.$refs.number,
      this.$refs.symbol
    ];

    checkboxes.forEach(checkbox => {
      checkbox.removeEventListener("click", this._disableOnlyCheckbox);
    });
  },
  watch: {
    length(value) {
      this.$refs.slider.style.background = `linear-gradient(90deg, ${
        this.sliderProps.fill
      } ${this._calculatePercentage(value)}%, ${
        this.sliderProps.background
      } ${this._calculatePercentage(value) + 0.1}%)`;
    }
  },
  computed: {
    sliderProps() {
      return {
        fill: "#0B1EDF",
        background: "rgba(255, 255, 255, 0.214)"
      };
    }
  }
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  width: 100%;
  height: 100vh;
  background-image: linear-gradient(to top, #209cff 100%, #68e0cf 200%);
  display: flex;
  justify-content: center;
  align-items: center;
}

button {
  border: 0;
  outline: 0;
}

.container {
  margin: 40px 0;
  width: 400px;
  height: 600px;
  padding: 10px 25px;
  background: #0a0e31;
  border-radius: 10px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.45), 0 4px 8px rgba(0, 0, 0, 0.35), 0 8px 12px rgba(0, 0, 0, 0.15);
  font-family: "Montserrat";
}
.container h2.title {
  font-size: 1.75rem;
  margin: 10px -5px;
  margin-bottom: 30px;
  color: #fff;
}

.result {
  position: relative;
  width: 100%;
  height: 65px;
  overflow: hidden;
}
.result__info {
  position: absolute;
  bottom: 4px;
  color: #fff;
  font-size: 0.8rem;
  transition: all 150ms ease-in-out;
  transform: translateY(200%);
  opacity: 0;
}
.result__info.right {
  right: 8px;
}
.result__info.left {
  left: 8px;
}
.result__viewbox {
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.08);
  border-radius: 8px;
  color: #fff;
  text-align: center;
  line-height: 65px;
}
.result #copy-btn {
  position: absolute;
  top: var(--y);
  left: var(--x);
  width: 38px;
  height: 38px;
  background: #fff;
  border-radius: 50%;
  opacity: 0;
  transform: translate(-50%, -50%) scale(0);
  transition: all 350ms cubic-bezier(0.175, 0.885, 0.32, 1.275);
  cursor: pointer;
  z-index: 2;
}
.result #copy-btn:active {
  box-shadow: 0 0 0 200px rgba(255, 255, 255, 0.08);
}
.result:hover #copy-btn {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1.35);
}

.field-title {
  position: absolute;
  top: -10px;
  left: 8px;
  transform: translateY(-50%);
  font-weight: 800;
  color: rgba(255, 255, 255, 0.5);
  text-transform: uppercase;
  font-size: 0.65rem;
  pointer-events: none;
  user-select: none;
}

.options {
  width: 100%;
  height: auto;
  margin: 50px 0;
}

.range__slider {
  position: relative;
  width: 100%;
  height: calc(65px - 10px);
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(255, 255, 255, 0.08);
  border-radius: 8px;
  margin: 30px 0;
}
.range__slider::before, .range__slider::after {
  position: absolute;
  color: #fff;
  font-size: 0.9rem;
  font-weight: bold;
}
.range__slider::before {
  content: attr(data-min);
  left: 10px;
}
.range__slider::after {
  content: attr(data-max);
  right: 10px;
}
.range__slider .length__title::after {
  content: attr(data-length);
  position: absolute;
  right: -16px;
  font-variant-numeric: tabular-nums;
  color: #fff;
}

#slider {
  -webkit-appearance: none;
  width: calc(100% - (70px));
  height: 2px;
  border-radius: 5px;
  background: rgba(255, 255, 255, 0.314);
  outline: none;
  padding: 0;
  margin: 0;
  cursor: pointer;
}
#slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  transition: all 0.15s ease-in-out;
}
#slider::-webkit-slider-thumb:hover {
  background: #d4d4d4;
  transform: scale(1.2);
}
#slider::-moz-range-thumb {
  width: 20px;
  height: 20px;
  border: 0;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  transition: background 0.15s ease-in-out;
}
#slider::-moz-range-thumb:hover {
  background: #d4d4d4;
}

.settings {
  position: relative;
  height: auto;
  widows: 100%;
  display: flex;
  flex-direction: column;
}
.settings .setting {
  position: relative;
  width: 100%;
  height: calc(65px - 10px);
  background: rgba(255, 255, 255, 0.08);
  border-radius: 8px;
  display: flex;
  align-items: center;
  padding: 10px 25px;
  color: #fff;
  margin-bottom: 8px;
}
.settings .setting input {
  opacity: 0;
  position: absolute;
}
.settings .setting input + label {
  user-select: none;
}
.settings .setting input + label::before, .settings .setting input + label::after {
  content: "";
  position: absolute;
  transition: 150ms cubic-bezier(0.24, 0, 0.5, 1);
  transform: translateY(-50%);
  top: 50%;
  right: 10px;
  cursor: pointer;
}
.settings .setting input + label::before {
  height: 30px;
  width: 50px;
  border-radius: 30px;
  background: rgba(214, 214, 214, 0.434);
}
.settings .setting input + label::after {
  height: 24px;
  width: 24px;
  border-radius: 60px;
  right: 32px;
  background: #fff;
}
.settings .setting input:checked + label:before {
  background: #5d68e2;
  transition: all 150ms cubic-bezier(0, 0, 0, 0.1);
}
.settings .setting input:checked + label:after {
  right: 14px;
}
.settings .setting input:focus + label:before {
  box-shadow: 0 0 0 2px rgba(255, 255, 255, 0.75);
}
.settings .setting input:disabled + label:before, .settings .setting input:disabled + label:after {
  cursor: not-allowed;
}
.settings .setting input:disabled + label:before {
  background: #4f4f6a;
}
.settings .setting input:disabled + label:after {
  background: #909090;
}

.btn.generate {
  user-select: none;
  position: relative;
  width: 100%;
  height: 50px;
  margin: 10px 0;
  border-radius: 8px;
  color: #fff;
  border: none;
  background-image: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  letter-spacing: 1px;
  font-weight: bold;
  text-transform: uppercase;
  cursor: pointer;
  transition: all 150ms ease;
}
.btn.generate:active {
  transform: translateY(-3%);
  box-shadow: 0 4px 8px rgba(255, 255, 255, 0.08);
}

.support {
  position: fixed;
  right: 10px;
  bottom: 10px;
  padding: 10px;
  display: flex;
}

a {
  margin: 0 20px;
  color: #fff;
  font-size: 2rem;
  transition: all 400ms ease;
}

a:hover {
  color: #222;
}

.github-corner svg {
  position: absolute;
  right: 0;
  top: 0;
  mix-blend-mode: darken;
  color: #eeeeee;
  fill: #353535;
  clip-path: polygon(0 0, 100% 0, 100% 100%);
}

.github-corner:hover .octo-arm {
  animation: octocat-wave 0.56s;
}

@keyframes octocat-wave {
  0%, 100% {
    transform: rotate(0);
  }
  20%, 60% {
    transform: rotate(-20deg);
  }
  40%, 80% {
    transform: rotate(10deg);
  }
}
</style>