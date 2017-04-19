<template>
  <div>
    <!--         删除todo 对话框 -->
    <el-dialog v-model="deleteDialogVisible" size="large" title="删除 TODO 吗?">
      <span slot="footer" class="dialog-footer">
        <el-button @click="deleteDialogVisible = false">取消</el-button>
        <el-button @click="remove(removeIndex)" type="primary">确认</el-button>
      </span>
    </el-dialog>
    <!--   新建一个todo -->
    <el-row type="flex" justify="center">
      <el-col :xs="22" :sm="18" :md="16" :lg="12">
        <transition name="fade" appear>
          <el-input  id="input" placeholder="新建一个TODO" v-model="input" icon="plus" :on-icon-click="add" @keyup.native.enter="add"></el-input>
        </transition>
      </el-col>
    </el-row>
    <!--   TODO Cards -->
    <el-row type="flex" justify="center">
      <el-col :xs="22" :sm="18" :md="16" :lg="12">
        <!--       No tasks / instructions -->
        <el-row v-if="items.length === 0" class="instruction">
          <transition name="fade" appear>
            <div>
              <h2>Jack's  <span style="color: #20A0FF">Todolist</span> 项目。</h2>
              <p>填写你的Todo开始行动吧</p>
            </div>
          </transition>
        </el-row>
        <!--       列出所有todolist -->
        <el-card v-else v-for="(item, index) in items" :class="item.classes" class="todo--card">
          <el-row type="flex">
            <!--   完成的todo -->
            <el-col class="todo todo--check">
              <i class="el-icon-check" @click="complete(index)"></i>
            </el-col>
            <!--    todo的项目 -->
            <el-col class="todo todo--item" @click.native="!items[index].edit ? edit(index) : null">
              <p v-if="!items[index].edit">{{ item.text }}</p>
              <!--  编辑区域-->
              <el-input v-else v-model.trim="items[index].text" @keyup.native.enter="edit(index)"></el-input>
            </el-col>
            <el-col class="todo todo--icon" style="display: flex">
              <i class="el-icon-edit" @click="edit(index)"></i>
              <i class="el-icon-close" @click="(deleteDialogVisible = true, removeIndex = index)"></i>
            </el-col>
          </el-row>
        </el-card>
        <el-row style="text-align: center; margin-top: 2em">
          <el-col>
            <transition name="fade-delay">
              <el-button type="primary" v-if="clearVisible" @click="clear">全部删除</el-button>
            </transition>
          </el-col>
        </el-row>
      </el-col>
    </el-row>
  </div>
</template>

<script>
class Item {
  constructor(text) {
    this.text = text;
    this.classes = {
      completed: false,
      'slide-in': true,
    };
    this.edit = false;
  }
}

export default {
  // 数据
  data () {
    return {
      clearVisible: false,
      deleteDialogVisible: false,
      input: null,
      items: [],
      removeIndex: null,
    }
  }, 
  computed: {
    incomplete() {
      // 所有已完成任务
      let incomplete = this.items.filter(el => {
        return el.classes.completed == false;
      });
      return incomplete;
    }
  },
  methods: {
    // 添加新的todo
    add() {
      if(this.input !== null) {
        let newItem = new Item(this.input.trim());
        this.items.push(newItem);
        this.input = null;
      } else {
        this.$message.warning('添加新的项目。');
      }
    },
    // 清除已完成任务
    clear() {
      this.items = [];
      this.clearVisible = false;
    },
    // 编辑完成项目
    complete(index) {
      let itemClass = this.items[index].classes;
      itemClass.completed = !itemClass.completed;
      itemClass['slide-in'] = !itemClass['slide-in'];
      // 移动新todo到数组的尾部
      //this.items.push(this.items.splice(index, 1)[0]);
      // 检查所有已完成项目, 如果全部完成给出提示
       if(this.incomplete.length === 0) {
        this.$message.success('干得漂亮!');
      }
    },
    // 编辑todo项目
    edit(index) {
      this.items[index].edit = !this.items[index].edit;
    },
    // 移除todo项目
    remove(index) {
      this.items.splice(index, 1);
      this.deleteDialogVisible = false;
    },
    showClear() {
      // 检测所有todo是否完成
      if(this.incomplete.length < 1 && this.items.length > 0) {
        this.clearVisible = true;
      } else {
        this.clearVisible = false;
      }
    },
    },
  watch: {
    incomplete() {
      // 监控所有todo是否都完成
      if(this.incomplete.length < 1 && this.items.length > 0) {
        this.clearVisible = true;
      } else {
        this.clearVisible = false;
      }
    },
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css?family=Roboto');

$extra-light-gray: #EFF2F7;
$green: #13CE66;
/*
@media (min-width: 1024px) {
.todo--card:not(.completed):hover {
background: $extra-light-gray;
}
}*/

body {
  font-family: 'Roboto', sans-serif;
  margin-top: 5em;
}

div {
  vertical-align: middle;
}

h2 {
  font-size: 22px;
  margin-bottom: 22px;
}

i {
  color: #bfcbd9;
  display: inline-block;
  transition: all .3s;
}

i:hover {
  cursor: pointer;
  color: #8391a5;
}

p {
  display: inline-block;
}

#input {
  margin-bottom: 2em;
}

.completed {
  animation: highlight 2s ease forwards;
}

.el-dialog__body {
  padding: 0;
}

.el-dialog__header {
  padding: 20px 20px 20px;
}

.el-dialog__footer {
  justify-content: center;
}

.el-icon-edit, .el-icon-close {
  padding-left: 15px;
}

.el-input, .el-tooltip__popper {
  font-size: 1em;
}

.el-input__icon {
  padding-right: 10px;
}

.fade-delay-enter, .fade-delay-leave-to {
  opacity: 0;
  transform: translatey(-30px);
}

.fade-delay-enter-active {
  transition: all .5s ease;
  transition-delay: 3s;
}

.fade-delay-leave-active {
  transition: all .5s ease;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
  transform: translatey(-30px);
}

.fade-enter-active, .fade-leave-active {
  transition: all .5s ease;
}

.instruction {
  text-align: center;
}

.slide-in {
  animation: slide-in .5s ease forwards;
}

.todo {
  align-self: center;
}

.todo--card {
  transition: all .3s;
  &:not(.completed):hover {
    background: $extra-light-gray;
  }
}

.todo--item {
  align-self: center;
  flex: 1 1 70%;
  margin: 0 .5em;
  &:hover {
    cursor: text;
  }
}

.todo--check, .todo--icon {
  width: auto;
}

[v-cloak] {
  display: none;
}

@keyframes highlight {
  0% {
    background-color: none;
    opacity: 1;
  }
  25% {
    background-color: $green;
    color: white;
    opacity: .88
  }
  50% {
    opacity: .66;
  }
  100% {
    background-color: none;
    opacity: .33;
  }
}

@keyframes slide-in {
  from {
    opacity: 0;
    transform: translatex(-50%);
  }
  to {
    opacity: 1;
    transform: translatex(0);
  }
}
</style>
