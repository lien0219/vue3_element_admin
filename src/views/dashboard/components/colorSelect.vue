<template>
  <div class="color-select">
    <div ref="saturation_value" class="saturation-value" @mousedown="mousedownColorPalette">
      <div :style="`background-color: hsl(${hue}, 100%, 50%);`">
        <div class="point" :style="pointStyle" />
      </div>
      <div class="saturation-value-2" />
      <div class="saturation-value-3" />
    </div>
    <div class="color-select-middle">
      <div class="color-slider">
        <div ref="hue_slider" class="hue-slider slider-item" @mousedown="mousedownHue">
          <div class="slider" :style="hueSliderStyle" />
        </div>
        <div
          v-if="props.alpha"
          ref="alpha_slider"
          class="alpha-slider slider-item"
          @mousedown="mousedownAlpha"
        >
          <div class="slider" :style="alphaSliderStyle" />
          <div
            :style="`background: linear-gradient(to right, rgba(0,0,0,0), ${colorEnums.rgb});width: 100%;height: 100%`"
          />
        </div>
      </div>
      <div class="color-diamond">
        <div
          :style="`background-color: ${colorEnums.rgba};width: 100%;height: 100%;box-shadow: inset 0 0 0 1px rgba(0, 0, 0, .15), inset 0 0 4px rgba(0, 0, 0, .25);`"
        />
      </div>
    </div>
    <div class="color-value">
      <div class="hex">
        <label>
          <input :value="colorEnums.hex8" spellcheck="false" @input="hexChange" />
        </label>
        <p>Hex</p>
      </div>
      <div class="rgba-r">
        <label>
          <input :value="red" @input="redChange" />
        </label>
        <p>R</p>
      </div>
      <div class="rgba-g">
        <label>
          <input :value="green" @input="greenChange" />
        </label>
        <p>G</p>
      </div>
      <div class="rgba-b">
        <label>
          <input :value="blue" @input="blueChange" />
        </label>
        <p>B</p>
      </div>
      <div v-if="props.alpha" class="rgba-a">
        <label>
          <input :value="alpha" @input="alphaChange" />
        </label>
        <p>A</p>
      </div>
    </div>
    <ul class="predefine">
      <li
        v-for="item in predefine"
        :key="item"
        class="predefine-item"
        :style="`background-color: ${item}`"
        @click="predefineChange(item)"
      />
    </ul>
    <div class="color-actions">
      <span class="cancel" @click="emits('close')">取消</span>
      <span class="confirm" @click="handleConfirm">确定</span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from "vue";

const props = defineProps({
  color: {
    type: Object || String,
    default() {
      return {
        r: 217,
        g: 128,
        b: 95,
        a: 1,
      };
    },
  },
  predefine: {
    type: Array,
    default() {
      return [];
    },
  },
  alpha: {
    type: Boolean,
    default: true,
  },
  mode: {
    type: String,
    default: "hex6", // hex6/hex8/rgb/rgba
  },
});

const emits = defineEmits(["update:color", "close"]);

const saturation_value = ref(null);
const hue_slider = ref(null);
const alpha_slider = ref(null);

let pointStyle = ref("top: 25%;left: 80%;");
let hueSliderStyle = ref("left: 0;");
let alphaSliderStyle = ref("left: calc(100% - 6px);");

let hue = ref(0);
let saturation = ref(1);
let value = ref(1);

let red = ref(255);
let green = ref(0);
let blue = ref(0);

let alpha = ref(1);

onMounted(() => {
  console.log("parseColor(props.color)", parseColor(props.color));

  let { r, g, b, a } = parseColor(props.color);
  red.value = r;
  green.value = g;
  blue.value = b;
  alpha.value = a;
});

watch([red, green, blue], () => {
  let { h, s, v } = rgb2hsv(red.value, green.value, blue.value);

  hue.value = h;
  saturation.value = s;
  value.value = v;

  // 移动背景板圆圈
  pointStyle.value = `top: ${100 - v * 100}%;left: ${s * 100}%;`;
  // 移动色调滑块
  hueSliderStyle.value = `left: ${(hue.value / 360) * 100}%;`;
});

watch(alpha, () => {
  // 移动透明度滑块
  alphaSliderStyle.value = `left: ${alpha.value >= 1 ? "calc(100% - 6px)" : alpha.value * 100 + "%"};`;
});

let colorEnums = computed(() => {
  let r = red.value;
  let g = green.value;
  let b = blue.value;
  let a = alpha.value;
  let h = hue.value;
  let s = saturation.value;
  let v = value.value;
  return {
    rgb: `rgba(${r},${g},${b})`,
    rgba: `rgba(${r},${g},${b},${a})`,
    hex6: rgba2hex(r, g, b),
    hex8: rgba2hex(r, g, b, a),
    hsv: `hsv(${h},${s},${v})`,
  };
});

// 确认选中的颜色值
const handleConfirm = () => {
  console.log("props.mode", props.mode);
  console.log("handleConfirm", colorEnums.value[props.mode]);
  emits("update:color", colorEnums.value[props.mode]);
};

// 输入框值变化,限制输入的值
function hexChange(e) {
  let v = e.target.value;
  if (/^#?([0-9a-fA-F]{6}|[0-9a-fA-F]{8})$/.test(v)) {
    let { r, g, b, a } = hex2rgba(v);
    red.value = r;
    green.value = g;
    blue.value = b;
    alpha.value = a;
  }
}

function redChange(e) {
  let v = e.target.value;
  if (v !== "") {
    v > 255 && (red.value = 255);
    v < 0 && (red.value = 0);
    v >= 0 && v <= 255 && (red.value = parseInt(v));
  }
}

function greenChange(e) {
  let v = e.target.value;
  if (v !== "") {
    v > 255 && (green.value = 255);
    v < 0 && (green.value = 0);
    v >= 0 && v <= 255 && (green.value = parseInt(v));
  }
}

function blueChange(e) {
  let v = e.target.value;
  if (v !== "") {
    v > 255 && (blue.value = 255);
    v < 0 && (blue.value = 0);
    v >= 0 && v <= 255 && (blue.value = parseInt(v));
  }
}

function alphaChange(e) {
  let v = e.target.value;
  if (v !== "") {
    v = parseFloat(v);
    alpha.value = v;
    v > 1 && (alpha.value = 1);
    v < 0 && (alpha.value = 0);
    v >= 0 && v <= 1 && (alpha.value = v);
  }
}

// 点击预设方块事件
function predefineChange(item) {
  if (/^#?([0-9a-fA-F]{6}|[0-9a-fA-F]{8})$/.test(item)) {
    let { r, g, b, a } = hex2rgba(item);
    red.value = r;
    green.value = g;
    blue.value = b;
    alpha.value = a;
  }
}

// 计算选中点的颜色值
function handleChangeColorPalette(e) {
  let w = saturation_value.value.clientWidth;
  let h = saturation_value.value.clientHeight;
  let x = e.pageX - saturation_value.value.getBoundingClientRect().left;
  let y = e.pageY - saturation_value.value.getBoundingClientRect().top;
  x = x < w && x > 0 ? x : x > w ? w : 0;
  y = y < h && y > 0 ? y : y > h ? h : 0;
  // 计算饱和度和亮度
  saturation.value = Math.floor((x / w) * 100 + 0.5) / 100;
  value.value = Math.floor((1 - y / h) * 100 + 0.5) / 100;
  // hsv转化为rgb
  let { r, g, b } = hsv2rgb(hue.value, saturation.value, value.value);
  red.value = r;
  green.value = g;
  blue.value = b;
  // 移动背景板圆圈
  pointStyle.value = `top: ${y}px;left: ${x}px;`;
}

function mousedownColorPalette(e) {
  // 鼠标按下计算饱和度和亮度并添加事件
  handleChangeColorPalette(e);
  // 添加整个页面的鼠标事件
  window.addEventListener("mousemove", handleChangeColorPalette);
  window.addEventListener("mouseup", mouseupColorPalette);
}

function mouseupColorPalette(e) {
  // 鼠标松开后移除事件
  window.removeEventListener("mousemove", handleChangeColorPalette);
  window.removeEventListener("mouseup", mouseupColorPalette);
}

// 色调
function handleChangeHue(e) {
  let w = hue_slider.value.clientWidth;
  let x = e.pageX - saturation_value.value.getBoundingClientRect().left;
  x = x < w && x > 0 ? x : x > w ? w : 0;
  // 计算色调
  hue.value = Math.floor((x / w) * 360 + 0.5);
  // hsv转化为rgb
  let { r, g, b } = hsv2rgb(hue.value, saturation.value, value.value);
  red.value = r;
  green.value = g;
  blue.value = b;
  // 移动滑块
  hueSliderStyle.value = `left: ${x >= w - 6 ? w - 6 : x}px;`;
}

function mousedownHue(e) {
  handleChangeHue(e);
  window.addEventListener("mousemove", handleChangeHue);
  window.addEventListener("mouseup", mouseupHue);
}

function mouseupHue(e) {
  window.removeEventListener("mousemove", handleChangeHue);
  window.removeEventListener("mouseup", mouseupHue);
}

// 透明度
function handleChangeAlpha(e) {
  let w = alpha_slider.value.clientWidth;
  let x = e.pageX - saturation_value.value.getBoundingClientRect().left;
  x = x < w && x > 0 ? x : x > w ? w : 0;
  // 计算透明度
  alpha.value = Math.floor((x / w) * 100 + 0.5) / 100;
  // 移动滑块
  alphaSliderStyle.value = `left: ${x >= w - 6 ? w - 6 : x}px;`;
}

function mousedownAlpha(e) {
  handleChangeAlpha(e);
  window.addEventListener("mousemove", handleChangeAlpha);
  window.addEventListener("mouseup", mouseupAlpha);
}

function mouseupAlpha(e) {
  window.removeEventListener("mousemove", handleChangeAlpha);
  window.removeEventListener("mouseup", mouseupAlpha);
}

/**
 * 解析输入的数据,只能解析hex颜色和rgb对象形式的数据
 * @param color
 */
function parseColor(color) {
  if (color) {
    let r, g, b, a;
    if (typeof color === "string") {
      if (/^#?([0-9a-fA-F]{6}|[0-9a-fA-F]{8}|[0-9a-fA-F]{3}|[0-9a-fA-F]{4})$/.test(color)) {
        return hex2rgba(color);
      } else if (color.includes("linear-gradient")) {
        console.log("111parseColor111", color);
        let matchColors = color.match(/#[0-9a-fA-F]{6}/g);
        console.log("matchColors", matchColors);
        let avgColor = getAvgColor(matchColors);
        console.log("avgColor", avgColor);
        return hex2rgba(avgColor);
      }
    } else {
      r = color.r > 255 ? 255 : color.r < 0 ? 0 : color.r;
      g = color.g > 255 ? 255 : color.g < 0 ? 0 : color.g;
      b = color.b > 255 ? 255 : color.b < 0 ? 0 : color.b;
      a = color.a > 1 ? 1 : color.a < 0 ? 0 : color.a;
      return { r, g, b, a };
    }
  } else {
    return null;
  }
}

function hsv2rgb(h, s, v) {
  h === 360 && (h = 0);
  let i = Math.floor(h / 60) % 6;
  let f = h / 60 - i;
  let p = v * (1 - s);
  let q = v * (1 - s * f);
  let t = v * (1 - s * (1 - f));
  let r, g, b;
  if (i === 0) {
    r = v;
    g = t;
    b = p;
  } else if (i === 1) {
    r = q;
    g = v;
    b = p;
  } else if (i === 2) {
    r = p;
    g = v;
    b = t;
  } else if (i === 3) {
    r = p;
    g = q;
    b = v;
  } else if (i === 4) {
    r = t;
    g = p;
    b = v;
  } else if (i === 5) {
    r = v;
    g = p;
    b = q;
  }
  r = Math.floor(r * 255 + 0.5);
  g = Math.floor(g * 255 + 0.5);
  b = Math.floor(b * 255 + 0.5);
  return { r, g, b };
}

function rgb2hsv(r, g, b) {
  let r1 = r / 255;
  let g1 = g / 255;
  let b1 = b / 255;
  let cmax = Math.max(r1, g1, b1);
  let cmin = Math.min(r1, g1, b1);
  let d = cmax - cmin;
  let h, s, v;
  if (d === 0) {
    h = 0;
  } else if (cmax === r1) {
    h = ((60 * (g1 - b1)) / d + 360) % 360;
  } else if (cmax === g1) {
    h = 60 * ((b1 - r1) / d + 2);
  } else if (cmax === b1) {
    h = 60 * ((r1 - g1) / d + 4);
  }
  if (cmax === 0) {
    s = 0;
  } else {
    s = d / cmax;
  }
  v = cmax;
  h = Math.floor(h + 0.5);
  s = Math.floor(s * 100 + 0.5) / 100;
  v = Math.floor(v * 100 + 0.5) / 100;
  return { h, s, v };
}

function rgba2hex(r, g, b, a = 1) {
  r = parseInt(r);
  let r1 = r.toString(16).length !== 2 ? "0" + r.toString(16) : r.toString(16);
  g = parseInt(g);
  let g1 = g.toString(16).length !== 2 ? "0" + g.toString(16) : g.toString(16);
  b = parseInt(b);
  let b1 = b.toString(16).length !== 2 ? "0" + b.toString(16) : b.toString(16);
  a = parseFloat(a);
  let a1 = "";
  if (a !== 1) {
    let temp = Math.floor(256 * a);
    a1 = temp.toString(16).length !== 2 ? "0" + temp.toString(16) : temp.toString(16);
  }
  return `#${r1}${g1}${b1}${a1}`.toUpperCase();
}

function hex2rgba(s) {
  console.log("111111", s);
  if (/^#?[0-9a-fA-F]{3}$/.test(s)) {
    let b = s.substring(s.length - 1, s.length);
    let g = s.substring(s.length - 2, s.length - 1);
    let r = s.substring(s.length - 3, s.length - 2);
    return hex2rgba(`${r + r}${g + g}${b + b}`);
  }
  if (/^#?[0-9a-fA-F]{4}$/.test(s)) {
    let a = s.substring(s.length - 1, s.length);
    let b = s.substring(s.length - 2, s.length - 1);
    let g = s.substring(s.length - 3, s.length - 2);
    let r = s.substring(s.length - 4, s.length - 3);
    return hex2rgba(`${r + r}${g + g}${b + b}${a + a}`);
  }
  if (/^#?[0-9a-fA-F]{6}$/.test(s)) {
    let b = parseInt("0x" + s.substring(s.length - 2, s.length));
    let g = parseInt("0x" + s.substring(s.length - 4, s.length - 2));
    let r = parseInt("0x" + s.substring(s.length - 6, s.length - 4));
    return { r, g, b, a: 1 };
  }
  if (/^#?[0-9a-fA-F]{8}$/.test(s)) {
    let a = parseInt("0x" + s.substring(s.length - 2, s.length));
    a = a / 255;
    let b = parseInt("0x" + s.substring(s.length - 4, s.length - 2));
    let g = parseInt("0x" + s.substring(s.length - 6, s.length - 4));
    let r = parseInt("0x" + s.substring(s.length - 8, s.length - 6));
    return { r, g, b, a };
  }
}

function getAvgColor(arr) {
  try {
    let parseColor = function (hexStr) {
      return hexStr.length === 4
        ? hexStr
            .substr(1)
            .split("")
            .map(function (s) {
              return 0x11 * parseInt(s, 16);
            })
        : [hexStr.substr(1, 2), hexStr.substr(3, 2), hexStr.substr(5, 2)].map(function (s) {
            return parseInt(s, 16);
          });
    };

    let pad = function (s) {
      return s.length === 1 ? "0" + s : s;
    };

    let gradientColors = function (start, end, steps, gamma) {
      let i;
      let j;
      let ms;
      let me;
      let output = [];
      let so = [];
      gamma = gamma || 1;
      let normalize = function (channel) {
        return Math.pow(channel / 255, gamma);
      };
      start = parseColor(start).map(normalize);
      end = parseColor(end).map(normalize);
      for (i = 0; i < steps; i++) {
        ms = i / (steps - 1);
        me = 1 - ms;
        for (j = 0; j < 3; j++) {
          so[j] = pad(
            Math.round(Math.pow(start[j] * me + end[j] * ms, 1 / gamma) * 255).toString(16)
          );
        }
        output.push("#" + so.join(""));
      }
      return output;
    };
    return gradientColors(arr[0], arr[1], 3)[1];
  } catch (err) {
    return arr[0];
  }
}
</script>

<style lang="scss" scoped>
.color-select {
  position: relative;
  width: 300px;
  padding: 10px;
  user-select: none;
  background: #fff;

  /* border: 1px solid #ccc; */

  /* border-radius: 10px; */
}

/* 饱和度和亮度 */
.saturation-value {
  position: relative;
  width: 100%;
  height: 200px;
  margin-bottom: 10px;
  cursor: pointer;
  box-shadow: 1px 1px 1px rgb(0 0 0 / 10%);
}

.saturation-value > div {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

/* 圆圈 */
.point {
  position: absolute;
  z-index: 9;
  box-sizing: border-box;
  width: 6px;
  height: 6px;
  background-color: transparent;
  border: 2px solid #ccc;
  border-radius: 50%;
  transform: translate(-50%, -50%);
}

.saturation-value-2 {
  background: linear-gradient(to right, white, #fff0);
}

.saturation-value-3 {
  background: linear-gradient(to top, black, #fff0);
}

/* 色调 透明度 */
.color-select-middle {
  display: flex;
  width: 100%;
  margin-bottom: 10px;
}

.slider-item + .slider-item {
  margin-top: 6px;
}

/* 色调滑块条 */
.hue-slider {
  position: relative;
  width: 100%;
  height: 10px;
  background: linear-gradient(90deg, red 0, #ff0 17%, #0f0 33%, #0ff 50%, #00f 67%, #f0f 83%, red);
  box-shadow: 1px 1px 1px rgb(0 0 0 / 10%);
}

/* 透明度滑块条 */
.alpha-slider {
  position: relative;
  width: 100%;
  height: 10px;
  background: #fff
    url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAWElEQVRIiWM8fubkfwYygKWJOSM5+mCAhRLNoxaPWjxq8ajFoxbTyeL/DAfJ0Xjs3Cl7Siwmu4Yht1aDgZEYx6MWj1o8avGoxaMWD3qLya5X//4nqx6HAQC7RBGFzolqTAAAAABJRU5ErkJggg==");
  background-size: 10px 10px;
  box-shadow: 1px 1px 1px rgb(0 0 0 / 10%);
}

/* 滑块 */
.slider {
  position: absolute;
  box-sizing: border-box;
  width: 6px;
  height: 100%;
  background-color: #fff;
  box-shadow: 0 0 2px rgb(0 0 0 / 60%);
}

.color-slider {
  display: flex;
  flex: auto;
  flex-wrap: wrap;
  align-items: center;
}

/* 颜色方块 */
.color-diamond {
  position: relative;
  width: 26px;
  height: 26px;
  margin-left: 5px;
  overflow: hidden;
  background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAWElEQVRIiWM8fubkfwYygKWJOSM5+mCAhRLNoxaPWjxq8ajFoxbTyeL/DAfJ0Xjs3Cl7Siwmu4Yht1aDgZEYx6MWj1o8avGoxaMWD3qLya5X//4nqx6HAQC7RBGFzolqTAAAAABJRU5ErkJggg==");
  background-size: 10px 10px;
  border-radius: 3px;
}

/* 颜色的值 hex rgba */
.color-value {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.color-value div {
  padding: 0 3px;
  text-align: center;
}

.color-value input {
  box-sizing: border-box;
  width: 34px;
  height: 24px;
  padding: 0;
  margin: 0;
  font-size: 12px;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 3px;
  outline: none;
}

.color-value p {
  margin: 3px 0 0;
  font-size: 12px;
}

.color-value .rgba-a {
  padding-right: 0;
}

.color-value .hex {
  flex: 1;
  padding-left: 0;
}

.color-value .hex input {
  width: 100%;
  height: 24px;
}

/* 预设颜色  */
.predefine {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
  width: 100%;
  padding: 0;
  margin: 10px 0 0;
  list-style: none;
}

.predefine-item {
  width: 20px;
  height: 20px;
  margin-bottom: 6px;
  border: 1px solid #ccc;
  border-radius: 6px;
}

.predefine-item + .predefine-item {
  margin-left: 6px;
}

.predefine-item:nth-child(12n) {
  margin-left: 0;
}

.color-actions {
  font-size: 12px;
  text-align: right;
}

.color-actions span {
  box-sizing: border-box;
  display: inline-block;
  padding: 5px 12px;
  line-height: 12px;
  border: 1px solid transparent;
}

.color-actions .cancel:hover {
  background-color: #f5f7fa;
}

.color-actions .confirm {
  margin-left: 10px;
  border-color: #dcdfe6;
  border-radius: 4px;
}

.color-actions .confirm:hover {
  color: #1677ff;
  border-color: #1677ff;
}
</style>
