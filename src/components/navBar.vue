<template>
  <div class="navbar" v-if="data.length > 0">
    <div class="list-content">
      <div class="list">
        <ul>
          <li
            v-for="(item, index) in data"
            :key="item.name"
            @mousedown="dragStart($event, index)"
            @mouseup="dragEnd($event)"
            @click="scrollTo(index)"
            :style="currentIndex === index ? dragStyles : null"
            :class="{
              moveup:
                isdraging && currentIndex < index && positionIndex >= index,
              movedown:
                isdraging && currentIndex > index && positionIndex <= index,
              active: mode === 'nav' && currentLi === index
            }"
          >
            {{ item.name }}
          </li>
        </ul>
      </div>
      <div
        class="button button_sort"
        @click="toggleMode"
        :class="{ active: mode === 'edit' }"
      >
        排序
      </div>
      <div class="button button_toTop" @click="scrollTo(0)">
        顶部
      </div>
    </div>
    <div class="bg" v-show="mode === 'edit'">
      <img
        src="https://s1.hdslb.com/bfs/static/jinkela/home/asserts/tab2233.png"
      />
    </div>
  </div>
</template>
<script>
import { throttle } from "lodash";
export default {
  name: "navBar",
  props: {
    data: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      currentIndex: -1,
      currentY: 0,
      moveY: 0,
      positionIndex: 0,
      isdraging: false,
      mode: "nav",
      top: 0
    };
  },
  mounted() {
    document.addEventListener("mousemove", this.dragMove, false);
    this.handleScroll = throttle(() => {
      this.top = document.documentElement.scrollTop;
    }, 500);
    document.addEventListener("scroll", this.handleScroll, false);
  },
  beforeDestroy() {
    document.removeEventListener("mousemove", this.dragMove, false);
    document.removeEventListener("scroll", this.handleScroll, false);
  },
  computed: {
    dragStyles() {
      return `
                position:relative;
                z-index:1;
				top:${this.moveY}px;
				backgroundColor: rgb(3, 117, 246);
                color: #fff;
                `;
    },
    currentLi() {
      let j = 0;
      for (let i = 0; i < this.data.length; i++) {
        if (this.top < 0) {
          return -1;
        }
        if (this.top >= this.data[i].offsetTop) {
          j = i;
        } else {
          return j;
        }
      }
      return j;
    }
  },
  methods: {
    dragMove(e) {
      if (!this.isdraging || this.mode === "nav") {
        return;
      }
      this.moveY = e.clientY - this.currentY;
      let targetIndex = this.positionIndex;
      if (this.moveY > 10) {
        targetIndex = this.currentIndex + Math.floor((this.moveY + 10) / 25);
      } else if (this.moveY < -10) {
        targetIndex = this.currentIndex + Math.floor((this.moveY + 10) / 25);
      }
      if (targetIndex < 0) {
        targetIndex = 0;
      } else if (targetIndex > this.data.length - 1) {
        targetIndex = this.data.length - 1;
      }

      this.positionIndex = targetIndex;
      e.preventDefault();
    },
    dragStart(e, index) {
      if (this.mode === "nav") {
        return;
      }
      this.isdraging = true;
      this.currentIndex = index;
      this.currentY = e.clientY;
      this.positionIndex = index;
    },
    dragEnd() {
      if (this.mode === "nav") {
        return;
      }
      this.isdraging = false;
      this.moveY = 0;
      this.$emit("update", {
        current: this.currentIndex,
        target: this.positionIndex
      });
      this.positionIndex = 0;
      this.currentIndex = null;
    },
    scroll() {},
    scrollTo(index) {
      if (this.mode !== "nav") {
        return;
      }
      window.scrollTo({ top: this.data[index].offsetTop, behavior: "smooth" });
    },
    toggleMode() {
      this.mode = this.mode === "nav" ? "edit" : "nav";
    }
  }
};
</script>
<style lang="scss" scoped>
.navbar {
  .list-content {
    .list {
      ul > li {
        width: 60px;
        height: 25px;
        text-align: center;
        line-height: 25px;
        font-size: 20px;
        margin: 0;
        user-select: none;
        transition: transform 0.3s;
        &:hover {
          background-color: rgb(3, 117, 246);
          color: #fff;
        }
        &.moveup {
          transform: translateY(-25px);
        }
        &.movedown {
          transform: translateY(25px);
        }
      }
    }
    .button {
      border-top: 1px solid #999;
      font-size: 20px;
      text-align: center;
      line-height: 25px;
      font-size: 20px;
      user-select: none;
    }
  }
  .bg {
    position: absolute;
    z-index: -1;
    top: -2em;
    bottom: -2em;
    left: -120px;
    right: -2em;
    border-radius: 5px;
    background-color: rgba($color: #fff, $alpha: 0.5);
    animation: ani 0.3s;
    @keyframes ani {
      0% {
        transform: scale(0.5);
        opacity: 0;
      }
      100% {
        transform: scale(1);
        opacity: 1;
      }
    }
    img {
      margin-top: 2em;
      margin-left: 0.5em;
    }
  }
  .active {
    background-color: rgb(3, 117, 246);
    color: #fff;
  }
}
</style>
