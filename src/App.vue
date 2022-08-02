<template>
  <div id="app">
    <div id="funcs">
      <!-- .lazy 不会马上触发，当输入内容失去焦点后触发 -->
      品牌名称：<input type="text" v-model.lazy="name" @keyup.enter="add" />
      <button @click="add">添加数据</button>
      搜索关键字：<input type="text" v-model="keyword" />
    </div>

    <table id="tb">
      <tr>
        <th>编号</th>
        <th>品牌名称</th>
        <th>创建时间</th>
        <th>操作</th>
      </tr>

      <tr v-show="empty">
        <td colspan="4">当前列表无数据</td>
      </tr>

      <!-- 遍历search方法的返回值数组 -->
      <tr v-for="item in search(keyword)" :key="item.id">
        <td>{{ item.id }}</td>
        <td>{{ item.name }}</td>
        <td>{{ item.ctime | dateFormat }}</td>
        <!-- prevent阻止默认事件，这里是防止a标签点完跳转或者变色 -->
        <td><a href="#" @click.prevent="del(item.id)">删除</a></td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [
        { id: 1, name: "霸王", ctime: new Date() },
        { id: 2, name: "伊卡璐", ctime: new Date() },
        { id: 3, name: "飘柔", ctime: new Date() },
        { id: 4, name: "舒肤佳", ctime: new Date() },
      ],
      idIndex: 5,
      name: "",
      keyword: "",
      empty: true, // 列表是否为空
    };
  },
  methods: {
    // 添加一个品牌
    add() {
      // 获取id和name数据
      // 获取创建时间，把上述的这些数据组装成一个对象
      var newBrand = { id: this.idIndex, name: this.name, ctime: new Date() };

      if (this.name.trim()) {
        // 把这个对象追加到list数组中
        this.list.push(newBrand);
      } else {
        alert("请输入合法名称");
      }

      // 让id加1
      this.idIndex++;

      // 清空输入框
      this.name = "";
    },

    // 删除一个品牌
    del(id) {
      // 方法一：some() 寻找至少一个符合条件的元素，用来做删除挺合适
      // 遍历数组，查找数组中的id匹配上传入的id，如果找到了就删除掉
      /* this.list.some((item, index) => {
        // 判断item.id是否与传进来的id一致
        if (item.id == id) {
          // 找到了要删除
          this.list.splice(index, 1);
          // 已经找到了，就停止遍历
          return true;
        }
      }); */

      // 方法二：every() 做删除不是很适合
      /* this.list.every((item, index) => {
        // 判断item.id是否与传进来的id一致
        if (item.id == id) {
          // 找到了要删除
          this.list.splice(index, 1);
          // 已经找到了，就停止遍历
          return false;
        } else {
          // 如果不符合就会走这里
          return true;
        }
      }); */

      // 以上两个方法，只要找到某一个元素就会停止遍历，在一些批量删除的场景，就不太适合了

      // 方法三：filter() 过滤掉所有不符合条件的元素 保留批量的元素
      /* this.list = this.list.filter((item) => {
        // 方法1：
        if (item.id == id) {
          // 这个元素要过滤掉
          return false;
        } else {
          // 用if方法，这个else不能少
          return true;
        }

        // 方法2：
        // return item.id != id;
      }); */

      // 方法四：findIndex() 直接要找到要删除元素的索引
      // 用index保存找到的索引
      let index = this.list.findIndex((item) => {
        return item.id == id;
      });
      // 删除对应索引的数据
      this.list.splice(index, 1);
    },

    // search方法，根据关键字，过滤list数据，返回一个新数组
    search(keyword) {
      // 1. 根据关键字，过滤list数据
      var result = this.list.filter((item) => {
        // 检查item.name中是否包含关键字
        // this.keyword 是一个变量，所以要用new RegExp()这种方式书写，而不是字面量的方式
        var reg = new RegExp(keyword, "g");
        // 把要检查的字符串传入test即可

        // console.log("reg.test(item.name)", reg.test(item.name));
        return reg.test(item.name);
      });

      // 判断result的长度，如果为0 把flag修改为false，否则为true
      this.empty = result.length > 0 ? false : true;
      return result;
    },
  },
  filters: {
    dateFormat(val) {
      var date = new Date(val);
      // console.log(date);

      var y = date.getFullYear().toString().padStart(2, "0");
      var m = (date.getMonth() + 1).toString().padStart(2, "0");
      var d = date.getDate().toString().padStart(2, "0");
      // padStart() 补零操作，先toString()
      var hh = date.getHours().toString().padStart(2, "0");
      var mm = date.getMinutes().toString().padStart(2, "0");
      var ss = date.getSeconds().toString().padStart(2, "0");

      return `${y}-${m}-${d} ${hh}:${mm}:${ss}`;
    },
  },
  computed: {
    /* newArrList() {
      this.list.filter((item) => {
        return item.name.indexOf(this.keyword) !== -1;
      });
    }, */
  },
};
</script>

<style scoped>
#tb {
  width: 800px;
  border-collapse: collapse;
  margin: 20px auto;
}

#tb th {
  background-color: royalblue;
  color: #fff;
  font-size: 16px;
  padding: 5px;
  text-align: center;
  border: 1px solid black;
}

#tb td {
  padding: 5px;
  text-align: center;
  border: 1px solid black;
}

#funcs {
  width: 800px;
  margin: 20px auto;
}
</style>