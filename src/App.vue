<template>
  <div id="app">
    <section class="content">
      <ul id="section_list" ref="sectionList">
        <li
          v-for="item in sectionData"
          :key="item.name"
          :style="bindColorStyle(item.color)"
        >
          {{ item.name }}
        </li>
      </ul>
    </section>
    <navBar :data="option" @update="handleUpdate($event)" />
  </div>
</template>

<script>
import navBar from "./components/navBar";
export default {
  name: "app",
  components: { navBar },
  data() {
    return {
      sectionData: [
        {
          number: 0,
          name: "直播",
          color: "AliceBlue"
        },
        {
          number: 1,
          name: "动画",
          color: "Aqua"
        },
        {
          number: 2,
          name: "番剧",
          color: "Aquamarine"
        },
        {
          number: 3,
          name: "国创",
          color: "Azure"
        },
        {
          number: 4,
          name: "漫画",
          color: "Beige"
        },
        {
          number: 5,
          name: "音乐",
          color: "Bisque"
        },
        {
          number: 6,
          name: "舞蹈",
          color: "BlanchedAlmond"
        },
        {
          number: 7,
          name: "游戏",
          color: "BlueViolet"
        },
        {
          number: 8,
          name: "科技",
          color: "BurlyWood"
        },
        {
          number: 9,
          name: "数码",
          color: "CadetBlue"
        },
        {
          number: 10,
          name: "生活",
          color: "Chartreuse"
        },
        {
          number: 11,
          name: "鬼畜",
          color: "Chocolate"
        },
        {
          number: 12,
          name: "时尚",
          color: "Coral"
        },
        {
          number: 13,
          name: "广告",
          color: "CornflowerBlue"
        },
        {
          number: 14,
          name: "娱乐",
          color: "Cornsilk"
        },
        {
          number: 15,
          name: "专栏",
          color: "Crimson"
        },
        {
          number: 16,
          name: "电影",
          color: "Cyan"
        },
        {
          number: 17,
          name: "TV剧",
          color: "DarkCyan"
        },
        {
          number: 18,
          name: "影视",
          color: "DarkGray"
        },
        {
          number: 19,
          name: "纪录片",
          color: "DarkKhaki"
        }
      ],
      heightList: []
    };
  },
  methods: {
    bindColorStyle(colorName) {
      return `background-color:${colorName}`;
    },
    handleUpdate(e) {
      const { current, target } = e;
      const copydata = [...this.sectionData];
      const cur_ele = this.sectionData[current];
      if (current < target) {
        copydata.splice(current, 1);
        copydata.splice(target, 0, cur_ele);
      } else if (current > target) {
        copydata.splice(current, 1);
        copydata.splice(target, 0, cur_ele);
      }
      this.sectionData = copydata;
      this.updateHeightList();
    },
    updateHeightList() {
      const list = this.$refs.sectionList;
      let heightList = null;
      if (list) {
        const lis = list.getElementsByTagName("li");
        heightList = Array.from(lis).map(item => {
          return item.offsetTop;
        });
      }
      this.heightList = heightList;
    }
  },
  computed: {
    option() {
      let i = 0;
      const option = this.sectionData.map(item => {
        return {
          name: item.name,
          offsetTop: this.heightList[i++]
        };
      });
      return option;
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.updateHeightList();
    });
  }
};
</script>

<style lang="scss">
@import "./style/reset.scss";
#app {
  .content {
    max-width: 1200px;
    margin: 0 auto;
    #section_list {
      li {
        height: 900px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 40px;
        margin: 20px 0;
      }
    }
  }
  .navbar {
    position: fixed;
    top: 100px;
    right: 50px;
    background-color: #fff;
  }
}
</style>
