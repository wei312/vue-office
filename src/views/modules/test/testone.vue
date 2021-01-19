<template>
  <!--<div>
    <div id="app">
      <label for="r1">修改颜色</label><input type="checkbox" v-model="use" id="r1">
      <br><br>
      <div v-bind:class="{'class1': use}">
        v-bind:class 指令{{use}}{{aa}}
        {{ok ? 'yes':'no'}}
      </div>
      <div v-if="use==true?false:true">你看不见我</div>
      <a :href="url">Click</a>
      <div v-for="moredata in moredatas">{{moredata.name}}</div>
    </div>
    <button v-on:click="counter++">点我{{counter}}</button>
  </div>-->
  <div class="content">
    <el-container>
      <el-header>
        <div class="head_left" style="display: inline-block">
          <div><a>新闻</a></div>
          <div><a>hao123</a></div>
          <div><a>地图</a></div>
          <div><a>视频</a></div>
          <div><a>贴吧</a></div>
          <div><a>学术</a></div>
          <div><a>更多</a></div>
          <template>
            <el-select v-model="value" placeholder="更多">
              <el-option
                v-for="item in cities"
                :key="item.value"
                :label="item.label"
                :value="item.value">
                <span style="float: left">{{ item.label }}</span>
                <span style="float: right; color: #8492a6; font-size: 13px">{{ item.value }}</span>
              </el-option>
            </el-select>
          </template>
        </div>
        <div class="head_right" style="float:right">
          <div><span>设置</span></div>
          <div><el-button style="background: #4e71f2;color: #FFFFFF;border-radius: 10px;">登录</el-button></div>
        </div>
      </el-header>
      <el-main>Main</el-main>
    </el-container>
  </div>
</template>


<script>
export default {
  name: 'testone',
  data () {
    return {
      use: false,
      aa: '123121',
      ok: true,
      url: 'www.baidu.com',
      moredatas: [
        {'name' : '小王'},
        {'name' : '妮妮'},
        {'name' : '雄安'},
      ],
      watch(){

      },
      counter: 0,
      cities: [{
        value: 'Beijing',
        label: '北京'
      }, {
        value: 'Shanghai',
        label: '上海'
      }, {
        value: 'Nanjing',
        label: '南京'
      }, {
        value: 'Chengdu',
        label: '成都'
      }, {
        value: 'Shenzhen',
        label: '深圳'
      }, {
        value: 'Guangzhou',
        label: '广州'
      }],
    }
  }
}
</script>

<style scoped>
.el-header, .el-footer {
  background-color: #FFFFFF;
  color: #333;

  line-height: 60px;
}
.head_left a{
  font-size: 13px;
  color: #222;
  cursor: pointer;
  text-decoration:none;
}
a:hover{color: #4e71f2}
.head_left div{
  display: inline-block;
  padding-left: 24px;
}
.head_right div{
  display: inline-block;
  padding-left: 24px;

}
.head_right span{

  cursor: pointer;
}
</style>
