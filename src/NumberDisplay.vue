<template>
  <svg :viewBox="viewBox" width="100%" height="100%" fill="#aaa">
    <rect x="-10%" y="-10%" width="120%" height="120%" :fill="this.digitSettings.color.background"></rect>
    <template v-for="(digit, digitIndex) in digits">
      <template v-for="(segment, segmentIndex) in digit.segments">
        <rect :x="segment.x + (digitIndex*100)"
              :y="segment.y"
              :width="segment.width"
              :height="segment.height"
              :rx="this.lineWidth /2 + 'px'"
              :fill="fill(segmentIndex, digit.value)"
        ></rect>
      </template>
    </template>
  </svg>
</template>
<script>
export default {
  props: {
    input: {
      type: String,
      default: ''
    },
    numberOfDigits: {
      type: Number,
      default: 8
    }
  },
  data() {
    return {
      height: 180,
      width: 100,
      lineWidth: 16,
      margin: 12
    }
  },
  computed: {
    viewBox() {
      let box = `-${this.margin} `
      box += '0 '
      box += `${this.width * this.numberOfDigits + this.margin * 2} `
      box += `${this.height}`
      return box
    },
    digitSettings() {
      return {
        color: {
          background: "#205",
          on: "rgba(255,50,50,1)",
          off: "rgba(255,50,50,0.1)"
        },
        marginPlusLineWidth: this.margin + this.lineWidth,

        verticalLineLength: (this.height - this.margin * 2 - this.lineWidth * 3) / 2,
        horizontalLineLength: this.width - this.margin * 2 - this.lineWidth * 2,

        topUpperVerticalLine: this.width - this.margin - this.lineWidth,
        topLowerVerticalLine: (this.height + this.lineWidth) / 2,
        bottomHorizontalLine: this.height - this.margin - this.lineWidth,
        middleVertical: (this.height - this.lineWidth) / 2,
      };
    },
    digits() {
      let digits = ('' + this.input).padStart(this.numberOfDigits, ' ').split('').slice(-this.numberOfDigits)

      return digits.map((src) => this.formatDigit(src))
    },
    segments() {
      return [
        { //dot
          x: this.width - this.lineWidth / 2,
          y: this.digitSettings.bottomHorizontalLine,
          width: this.lineWidth,
          height: this.lineWidth
        },
        { //top
          x: this.digitSettings.marginPlusLineWidth,
          y: this.margin,
          width: this.digitSettings.horizontalLineLength,
          height: this.lineWidth
        }, { //upper right
          x: this.digitSettings.topUpperVerticalLine,
          y: this.digitSettings.marginPlusLineWidth,
          width: this.lineWidth,
          height: this.digitSettings.verticalLineLength
        }, { //lower right
          x: this.digitSettings.topUpperVerticalLine,
          y: this.digitSettings.topLowerVerticalLine,
          width: this.lineWidth,
          height: this.digitSettings.verticalLineLength
        }, { //bottom
          x: this.digitSettings.marginPlusLineWidth,
          y: this.digitSettings.bottomHorizontalLine,
          width: this.digitSettings.horizontalLineLength,
          height: this.lineWidth
        }, { //lower left
          x: this.margin,
          y: this.digitSettings.topLowerVerticalLine,
          width: this.lineWidth,
          height: this.digitSettings.verticalLineLength
        }, { //upper left
          x: this.margin,
          y: this.digitSettings.marginPlusLineWidth,
          width: this.lineWidth,
          height: this.digitSettings.verticalLineLength
        }, { //center
          x: this.digitSettings.marginPlusLineWidth,
          y: this.digitSettings.middleVertical,
          width: this.digitSettings.horizontalLineLength,
          height: this.lineWidth
        },
      ];
    }
  },
  methods: {
    formatDigit(digit) {
      return {
        value: this.parseValue(digit),
        segments: this.segments,
      }
    },
    parseValue(value) {
      let result = ''

      if (['-','_','.',' '].indexOf(value) >= -1) {
        result = value
      } else {
        result = parseInt(value)
        if (!result && result !== 0) {
          result = ' '
        }
      }
      return result;
    },
    hexValue(value) {
      switch (value) {
        case '0':
          return 0x7E;
        case '1':
          return 0x30;
        case '2':
          return 0x6D;
        case '3':
          return 0x79;
        case '4':
          return 0x33;
        case '5':
          return 0x5B;
        case '6':
          return 0x5F;
        case '7':
          return 0x70;
        case '8':
          return 0x7F;
        case '9':
          return 0x7B;
        case '.':
          return 0x80;
        case ' ':
          return 0x00;
        case '-':
          return 0x01;
        case '_':
          return 0x08;
      }
    },
    fill(segmentIndex, value) {
      const isOn = this.hexValue(value) >> (7 - segmentIndex) & 1
      return isOn ? this.digitSettings.color.on : this.digitSettings.color.off
    }
  }
}
</script>
