<script setup>
import { ref, onMounted } from "vue";

import backgroundTop from "../assets/img/background-top.jpeg";
import backgroundBottom from "../assets/img/background-bottom.jpeg";
import backgroundLeft from "../assets/img/background-left.jpeg";
import backgroundRight from "../assets/img/background-right.jpeg";

import frame02Top from "../assets/img/frame02-top.jpg";
import frame02Bottom from "../assets/img/frame02-bottom.jpg";
import frame02Left from "../assets/img/frame02-left.jpg";
import frame02Right from "../assets/img/frame02-right.jpg";

import frame03Top from "../assets/img/frame03-top.jpg";
import frame03Bottom from "../assets/img/frame03-bottom.jpg";
import frame03Left from "../assets/img/frame03-left.jpg";
import frame03Right from "../assets/img/frame03-right.jpg";

import coverImage from "../assets/img/cover.jpeg";

defineProps({});

const heightCanvas = 800;
const widthCanvas = 800;
const IMAGE_TYPES = [
  "jpg",
  "gif",
  "jpeg",
  "bmp",
  "png",
  "image/jpeg",
  "image/png",
];
const LIMIT_SIZE_MEDIA = 20000000;
const SOURCE_FRAME = [
  {
    top: backgroundTop,
    bottom: backgroundBottom,
    left: backgroundLeft,
    right: backgroundRight,
  },
  {
    top: frame02Top,
    bottom: frame02Bottom,
    left: frame02Left,
    right: frame02Right,
  },
  {
    top: frame03Top,
    bottom: frame03Bottom,
    left: frame03Left,
    right: frame03Right,
  },
];

const selectedFrame = ref({});
const selectedBackground = ref("#fff");
const selectedImage = ref();
const frameSource = ref(SOURCE_FRAME[0]);

const layer = new Konva.Layer();
let stage;

const frameImageTop = new Image();
const frameImageBottom = new Image();
const frameImageLeft = new Image();
const frameImageRight = new Image();
const frameCoverImage = new Image();

onMounted(() => {
  stage = new Konva.Stage({
    container: "container-frame",
    width: widthCanvas,
    height: heightCanvas,
  });

  stage.add(layer);
  frameCoverImage.src = coverImage;
  frameImageTop.src = backgroundTop;
  frameImageBottom.src = backgroundBottom;
  frameImageLeft.src = backgroundLeft;
  frameImageRight.src = backgroundRight;
  frameCoverImage.src = coverImage;

  const widthInput = document.getElementById("widthInput");
  const heightInput = document.getElementById("heightInput");
  const imageInput = document.getElementById("imageInput");
  const frameSelect = document.getElementById("frameSelect");
  const backgroundSelect = document.getElementById("backgroundSelect");

  widthInput.addEventListener("change", updateCanvas);
  widthInput.addEventListener("input", updateCanvas);

  heightInput.addEventListener("change", updateCanvas);
  heightInput.addEventListener("input", updateCanvas);

  imageInput.addEventListener("change", updateCanvas);
  imageInput.addEventListener("input", updateCanvas);

  frameSelect.addEventListener("change", updateCanvas);
  frameSelect.addEventListener("input", updateCanvas);

  backgroundSelect.addEventListener("change", updateCanvas);
  backgroundSelect.addEventListener("input", updateCanvas);

  // set default value
  widthInput.value = 1000;
  heightInput.value = 1300;
  imageInput.value = 350;
  frameSelect.value = 1;
  backgroundSelect.value = selectedBackground.value ?? "#fff";

  setTimeout(() => {
    updateCanvas();
  }, 300);
});

const changeFrame = async () => {
  frameSource.value = SOURCE_FRAME[selectedFrame.value - 1];
  setTimeout(() => {
    updateCanvas();
  }, 100);
};

const changeBackground = (e) => {};

function createFrame(frameWidth, frameHeight, imageInsertSize) {
  const padding = frameImageTop.height;

  frameImageTop.src = frameSource.value?.top ?? backgroundTop;
  frameImageBottom.src = frameSource.value?.bottom ?? backgroundBottom;
  frameImageLeft.src = frameSource.value?.left ?? backgroundLeft;
  frameImageRight.src = frameSource.value?.right ?? backgroundRight;

  frameCoverImage.src = selectedImage.value ?? coverImage;

  const paddingTop = frameImageTop.height;
  const paddingBottom = frameImageBottom.height;
  const paddingLeft = frameImageLeft.width;
  const paddingRight = frameImageRight.width;

  console.log(
    "top: ",
    paddingTop,
    "bottom: ",
    paddingBottom,
    "left: ",
    paddingLeft,
    "right: ",
    paddingRight
  );
  const group = new Konva.Group();

  const top = new Konva.Line({
    points: [
      0,
      0,
      frameWidth,
      0,
      frameWidth - paddingTop,
      paddingTop,
      paddingTop,
      paddingTop,
    ],
    strokeWidth: 0,
    hitStrokeWidth: 0,
    lineJoin: 0,
    strokeEnabled: false,
    shadowColor: "black",
    shadowBlur: 15,
    shadowOffset: { x: 30, y: 30 },
    shadowOpacity: 0.3,
    fillPatternImage: frameImageTop,
    // fillPatternScaleY: frameHeight / frameImageTop.height,
    // fillPatternScaleX: frameWidth / frameImageTop.width,
  });

  const left = new Konva.Line({
    points: [
      0,
      0,
      paddingLeft,
      paddingLeft,
      paddingLeft,
      frameHeight - paddingLeft,
      0,
      frameHeight,
    ],
    strokeWidth: 0,
    hitStrokeWidth: 0,
    lineJoin: 0,
    strokeEnabled: false,
    shadowColor: "black",
    shadowBlur: 15,
    shadowOffset: { x: 30, y: 30 },
    shadowOpacity: 0.3,
    fillPatternImage: frameImageLeft,
    // fillPatternScaleX: frameWidth / frameImageTop.width,
  });

  const bottom = new Konva.Line({
    points: [
      0,
      frameHeight,
      paddingBottom,
      frameHeight - paddingBottom,
      frameWidth - paddingBottom,
      frameHeight - paddingBottom,
      frameWidth,
      frameHeight,
    ],
    strokeWidth: 0,
    hitStrokeWidth: 0,
    lineJoin: 0,
    strokeEnabled: false,
    shadowColor: "black",
    shadowBlur: 10,
    shadowOffset: { x: 30, y: 30 },
    shadowOpacity: 0.2,
    fillPatternImage: frameImageBottom,
  });

  const right = new Konva.Line({
    points: [
      frameWidth,
      0,
      frameWidth,
      frameHeight,
      frameWidth - paddingRight,
      frameHeight - paddingRight,
      frameWidth - paddingRight,
      paddingRight,
    ],
    strokeWidth: 0,
    hitStrokeWidth: 0,
    lineJoin: 0,
    strokeEnabled: false,
    shadowColor: "black",
    shadowBlur: 10,
    shadowOffset: { x: 30, y: 30 },
    shadowOpacity: 0.2,
    fillPatternImage: frameImageRight,
  });

  const oval = new Konva.Ellipse({
    x: frameWidth / 2,
    y: frameHeight / 2,
    radiusX: imageInsertSize * 0.7,
    radiusY: imageInsertSize,
    stroke: "black",
    strokeWidth: 0,
    hitStrokeWidth: 0,
    lineJoin: 0,
    fillPatternImage: frameCoverImage,
    fillPatternRepeat: "no-repeat",
    fillPatternOffsetX: imageInsertSize,
    fillPatternOffsetY: imageInsertSize,
  });

  // background
  const glass = new Konva.Rect({
    x: padding,
    y: padding,
    width: frameWidth - padding * 2,
    height: frameHeight - padding * 2,
    fill: selectedBackground.value ?? "white",
    shadowColor: "black",
    shadowBlur: 30,
    shadowOffset: { x: -30, y: -30 },
    shadowOpacity: 0.2,
  });

  group.add(glass, top, left, bottom, right, oval);

  group.find("Line").forEach((line) => {
    line.closed(true);
    line.stroke("black");
    line.strokeWidth(1);
  });

  return group;
}
function createInfo(frameWidth, frameHeight) {
  const offset = 80;

  const arrowOffset = offset / 2;
  const arrowSize = 10;

  const group = new Konva.Group();
  const lines = new Konva.Shape({
    sceneFunc: function (ctx) {
      ctx.fillStyle = "grey";
      ctx.lineWidth = 0.5;

      ctx.moveTo(0, 0);
      ctx.lineTo(0, 0);

      ctx.moveTo(0, frameHeight);
      ctx.lineTo(0, frameHeight);

      ctx.moveTo(0, frameHeight);
      ctx.lineTo(0, frameHeight + 0);

      ctx.moveTo(frameWidth, frameHeight);
      ctx.lineTo(frameWidth, frameHeight + 0);

      ctx.stroke();
    },
  });

  const leftArrow = new Konva.Shape({
    sceneFunc: function (ctx) {
      // top pointer
      ctx.moveTo(-arrowOffset - arrowSize, arrowSize);
      ctx.lineTo(-arrowOffset, 0);
      ctx.lineTo(-arrowOffset + arrowSize, arrowSize);

      // line
      ctx.moveTo(-arrowOffset, 0);
      ctx.lineTo(-arrowOffset, frameHeight);

      // bottom pointer
      ctx.moveTo(-arrowOffset - arrowSize, frameHeight - arrowSize);
      ctx.lineTo(-arrowOffset, frameHeight);
      ctx.lineTo(-arrowOffset + arrowSize, frameHeight - arrowSize);

      ctx.strokeShape(this);
    },
    stroke: "grey",
    strokeWidth: 0.5,
  });

  const bottomArrow = new Konva.Shape({
    sceneFunc: function (ctx) {
      // top pointer
      ctx.translate(0, frameHeight + arrowOffset);
      ctx.moveTo(arrowSize, -arrowSize);
      ctx.lineTo(0, 0);
      ctx.lineTo(arrowSize, arrowSize);

      // line
      ctx.moveTo(0, 0);
      ctx.lineTo(frameWidth, 0);

      // bottom pointer
      ctx.moveTo(frameWidth - arrowSize, -arrowSize);
      ctx.lineTo(frameWidth, 0);
      ctx.lineTo(frameWidth - arrowSize, arrowSize);

      ctx.strokeShape(this);
    },
    stroke: "grey",
    strokeWidth: 0.5,
  });

  // left text
  const leftLabel = new Konva.Label();

  leftLabel.add(
    new Konva.Tag({
      fill: "white",
    })
  );
  const leftText = new Konva.Text({
    text: heightInput.value,
    padding: 2,
    fill: "black",
  });

  leftLabel.add(leftText);
  leftLabel.position({
    x: -arrowOffset - leftText.width(),
    y: frameHeight / 2 - leftText.height() / 2,
    translate: 50,
  });

  leftLabel.on("click tap", function () {
    createInput("height", this.getAbsolutePosition(), leftText.size());
  });

  // bottom text
  const bottomLabel = new Konva.Label();

  bottomLabel.add(
    new Konva.Tag({
      fill: "white",
    })
  );
  const bottomText = new Konva.Text({
    text: widthInput.value,
    padding: 2,
    fill: "black",
  });

  bottomLabel.add(bottomText);
  bottomLabel.position({
    x: frameWidth / 2 - bottomText.width() / 2,
    y: frameHeight + arrowOffset,
  });

  bottomLabel.on("click tap", function () {
    createInput("width", this.getAbsolutePosition(), bottomText.size());
  });

  group.add(lines, leftArrow, bottomArrow, leftLabel, bottomLabel);

  return group;
}
function createInput(metric, pos, size) {
  const wrap = document.createElement("div");
  wrap.style.position = "absolute";
  wrap.style.backgroundColor = "rgba(0,0,0,0.1)";
  wrap.style.top = 0;
  wrap.style.left = 0;
  wrap.style.width = "100%";
  wrap.style.height = "100%";

  document.body.appendChild(wrap);

  const input = document.createElement("input");
  input.type = "number";

  const similarInput = metric === "width" ? widthInput : heightInput;
  input.value = similarInput.value;

  input.style.position = "absolute";
  input.style.top = pos.y + 3 + "px";
  input.style.left = pos.x + "px";

  input.style.height = size.height + 3 + "px";
  input.style.width = size.width + 25 + "px";

  wrap.appendChild(input);

  input.addEventListener("change", function () {
    similarInput.value = input.value;
    updateCanvas();
  });

  input.addEventListener("input", function () {
    similarInput.value = input.value;
    updateCanvas();
  });

  wrap.addEventListener("click", function (e) {
    if (e.target === wrap) {
      document.body.removeChild(wrap);
    }
  });

  input.addEventListener("keyup", function (e) {
    if (e.keyCode === 13) {
      document.body.removeChild(wrap);
    }
  });
}
function updateCanvas() {
  layer.children.forEach((child) => child.destroy());

  const frameWidth = parseInt(widthInput.value, 10);
  const frameHeight = parseInt(heightInput.value, 10);
  const imageInsertSize = parseInt(imageInput.value, 10);

  const wr = stage.width() / frameWidth;
  const hr = stage.height() / frameHeight;

  const ratio = Math.min(wr, hr) * 0.7;

  const frameOnScreenWidth = frameWidth * ratio;
  const frameOnScreenHeight = frameHeight * ratio;

  const group = new Konva.Group({});

  group.x(Math.round(stage.width() / 2 - frameOnScreenWidth / 2) + 0.5);
  group.y(Math.round(stage.height() / 2 - frameOnScreenHeight / 2) + 0.5);

  layer.add(group);

  const frameGroup = createFrame(frameWidth, frameHeight, imageInsertSize);

  frameGroup.scale({ x: ratio, y: ratio });
  group.add(frameGroup);

  const infoGroup = createInfo(frameOnScreenWidth, frameOnScreenHeight);
  group.add(infoGroup);
}

const selectImage = (file) => {
  if (file) {
    const { type, size } = file.target.files[0];

    const isValid = IMAGE_TYPES.includes(type);
    const isValidSize = size <= LIMIT_SIZE_MEDIA;
    if (!isValid) {
      // message.error(`写真形式が不正です。再確認ください。`);
      console.log("isn't valid file type");
    }
    if (!isValidSize) {
      // message.error(`1ファイルにつき20MBまでアップロードしてください。`);
      console.log("isn't valid size of file");
    }
    if (isValid || isValidSize) {
      let src = "";
      const reader = new FileReader();
      reader.onload = function () {
        src = window.URL.createObjectURL(file.target.files[0]);
        selectedImage.value = src;
        updateCanvas();
      };
      reader.readAsDataURL(file.target.files[0]);
    }
  }
};
</script>

<template>
  <div id="container-frame"></div>
  <div id="controls">
    <div class="item-input" style="">
      Width:
      <input type="number" id="widthInput" />
    </div>
    <div class="item-input">
      Height:
      <input type="number" id="heightInput" />
    </div>
    <div class="item-input">
      Image:
      <input type="number" id="imageInput" />
    </div>
    <div class="item-input">
      Frame:
      <select v-model="selectedFrame" @change="changeFrame" id="frameSelect">
        <option disabled value="">Please select one</option>
        <option :value="1">A</option>
        <option :value="2">B</option>
        <option :value="3">C</option>
      </select>
    </div>
    <div class="item-input">
      Background:
      <select
        v-model="selectedBackground"
        @change="changeBackground"
        id="backgroundSelect"
      >
        <option disabled value="">Please select one</option>
        <option :value="'#000'">Black</option>
        <option :value="'#fff'">White</option>
        <option :value="'#DBDDD0'">#DBDDD0</option>
      </select>
    </div>
    <div class="item-input">
      Select image:
      <input
        id="imageSelect"
        type="file"
        name="file"
        @change="selectImage"
        accept="image/*"
      />
    </div>
  </div>
</template>

<style scoped>
#frameSimulatorCanvas {
  box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
  border-radius: 10px;
}
#controls {
  display: flex;
}
.item-input {
  float: left;
  padding: 10px;
  color: black;
}
</style>
