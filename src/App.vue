<template>
  <header>
    用户管理
  </header>
  <section>
    <div class="content">
      <div class="content_top">
        <div class="left">
          <button @click="changeIsShow">新建</button>
          <input type="text" placeholder="搜索" v-model="searchWord" @keyup.enter="search"><button @click.prevent="search">搜索</button>
        </div>
        <div class="right">
          <button @click="originInfo">撤销</button>
        </div>
      </div>
      <div class="content_middle">
        <table>
          <thead>
            <tr>
              <th>姓名</th>
              <th>年龄</th>
              <th>性别</th>
              <th>联系电话</th>
              <th>详细地址</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td colspan="6" v-show="state.data.length==0" style="text-align:center;">目前没有数据噢~</td>
            </tr>
            <tr v-for="(item, index) in state.data" :key="index">
              <td>
                <input type="checkbox" v-model="item.isChecked"><span>{{item.name}}</span>
              </td>
              <td>{{item.age}}</td>
              <td>{{item.sex}}</td>
              <td>{{item.phone}}</td>
              <td>{{item.address_before}}{{item.address_after}}</td>
              <td>
                <button @click="changeInfo(item, index)">编辑</button>
                <button @click="deleteInfo(index)">删除</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="content_bottom">
        <button @click="deleteChecked" :disabled="isNoChecked">批量删除</button>
      </div>
    </div>
  </section>
  <AddOrUpdate v-show="isShow" ref="hello" @addOrUpdateDate="addOrUpdateDate" @cancel="cancel"></AddOrUpdate>
</template>

<script>
import { ref, reactive, onMounted, onBeforeUnmount, computed, toRaw } from 'vue';
import AddOrUpdate from '@/component/addUpdate';
export default {
  components: { AddOrUpdate },
  setup() {
    let searchWord = ref('');
    let hello = ref(null);
    let isShow = ref(false);
    let state = reactive({
      data: [
        {
          isChecked: false,
          name: 'a',
          age: 18,
          sex: '男',
          phone: '12332423',
          address_before: '北京市海淀区',
          address_after: 'xx街道xx号'
        },
        {
          isChecked: false,
          name: 'b',
          age: 23,
          sex: '女',
          phone: '12332423',
          address_before: '北京市丰台区',
          address_after: 'xx街道xx号'
        },
        {
          isChecked: false,
          name: 'c',
          age: 32,
          sex: '男',
          phone: '12332423',
          address_before: '广东省广州市天河区',
          address_after: 'xx街道xx号'
        }
      ],
      origin: []
    });
    onMounted(() => {
      getInfo();
    });
    onBeforeUnmount(() => {
      sessionStorage.clear();
    });

    const changeIsShow = () => {
      isShow.value = true;
    };

    const getInfo = () => {
      if (sessionStorage.getItem('info')) {
        state.data = JSON.parse(sessionStorage.getItem('info'));
      }
    };

    const deleteInfo = (index) => {
      rememberInfo();
      state.data.splice(index, 1);
      sessionStorage.setItem('info', JSON.stringify(state.data));
    };

    const rememberInfo = () => {
      state.origin = [];
      state.origin.push(...state.data);
    };

    const originInfo = () => {
      if (state.origin.length !== 0) {
        state.data = state.origin;
      }
    };

    const deleteChecked = () => {
      rememberInfo();
      for (let i = state.data.length - 1; i >= 0; i--) {
        if (state.data[i].isChecked == true) {
          state.data.splice(i, 1);
        }
      }
      sessionStorage.setItem('info', JSON.stringify(state.data));
    };

    const addOrUpdateDate = (obj) => {
      rememberInfo();
      isShow.value = obj.isShow;
      obj.form.isChecked = false;
      if(obj.index>=0){
        state.data.splice(obj.index, 1, obj.form);
      }else {
        state.data.push(obj.form);
      }
      sessionStorage.setItem('info', JSON.stringify(state.data));
    };

    const cancel = (val) => {
      isShow.value = val;
    };

    const changeInfo = (data, index) => {
      isShow.value = true;
      let val = {data, index}
      hello.value.getUserInfo(val);
    }

    const isNoChecked = computed(()=>{
      return state.data.every(item=>{
        return item.isChecked === false;
      })
    })

    const search = () => {
      rememberInfo();
      let ds = toRaw(state.data);
      let reg = new RegExp(searchWord.value);
      state.data = ds.filter(item=>{
        return item.name==searchWord.value || item.age==searchWord.value || item.sex==searchWord.value || item.phone==searchWord.value || reg.test(item.address_before) ||  reg.test(item.address_after);
      }) 
    }

    return {
      searchWord,
      hello,
      isShow,
      state,
      rememberInfo,
      originInfo,
      getInfo,
      deleteInfo,
      deleteChecked,
      addOrUpdateDate,
      changeIsShow,
      cancel,
      changeInfo,
      isNoChecked,
      search
    };
  }
};
</script>

<style lang="less">
* {
  margin: 0;
  padding: 0;
}
header {
  width: 100%;
  height: 80px;
  line-height: 80px;
  background-color: #ccc;
  text-align: center;
}
button {
  width: 80px;
  height: 26px;
  background-color: #ccc;
  border: 0;
  box-shadow: 2px 2px 3px #aaa;
  cursor: pointer;
  &:hover {
    background-color: #bbb;
  }
  &:disabled {
    cursor: default;
    background-color: #ccc;
  }
}
input {
  outline-style: none;
}
section {
  width: 1300px;
  margin: 0 auto;
  .content {
    margin-top: 30px;
    .content_top {
      overflow: hidden;
      input {
        height: 20px;
        width: 100px;
        padding-left: 5px;
      }
      .left {
        float: left;
        input {
          margin: 0 3px 0 40px;
        }
      }
      .right {
        float: right;
      }
    }
    .content_middle {
      table {
        width: 100%;
        margin: 30px auto;
        border-collapse: collapse;
        border: 1px solid #ccc;
        th {
          min-width: 100px;
          height: 35px;
          background-color: #ccc;
          &:nth-child(4) {
            min-width: 180px;
          }
          &:nth-child(5) {
            min-width: 220px;
          }
        }
        td {
          min-width: 100px;
          height: 35px;
          &:nth-child(1) {
            box-sizing: border-box;
            padding-left: 50px;
            text-align: left;
            input {
              vertical-align: middle;
            }
            span {
              margin-left: 10px;
            }
          }
          &:nth-child(4) {
            min-width: 180px;
          }
          &:nth-child(5) {
            min-width: 220px;
          }
          text-align: center;
          button {
            margin: 0 3px;
          }
        }
      }
    }
  }
}
</style>