<template>
  <div class="mask">
    <div class="content">
      <div class="top">
        <h3>新建/编辑用户</h3>
      </div>
      <div class="middle">
        <el-form :inline="true" :model="state.formInline" class="demo-form-inline" label-position="top" label-width="80px" ref="ruleForm">
          <el-form-item prop="name" label="姓名" :rules="{
          required: true,
          message: '姓名不能为空',
          trigger: 'blur',
        }">
            <el-input style="width: 214px;" v-model="state.formInline.name" placeholder="请输入用户姓名" clearable />
          </el-form-item>
          <el-form-item prop="age"  label="年龄" :rules="[{
          required: true,
          message: '年龄不能为空',
          trigger: 'blur',
        }]">
            <el-input v-model="state.formInline.age" placeholder="请输入用户年龄" clearable />
          </el-form-item>
          <el-form-item prop="sex"  label="性别" :rules="[{
          required: true,
          message: '性别不能为空',
          trigger: 'blur',
        }]">
            <el-select v-model="state.formInline.sex" placeholder="请输入用户性别">
              <el-option label="男" value="男" />
              <el-option label="女" value="女" />
            </el-select>
          </el-form-item>
          <el-form-item prop="phone" label="电话" :rules="[{
          required: true,
          message: '电话不能为空',
          trigger: 'blur',
        }]">
            <el-input v-model="state.formInline.phone" placeholder="请输入用户联系电话" clearable />
          </el-form-item>
          <el-form-item prop="address_after" label="详细地址" :rules="[{
          required: true,
          message: '地址不能为空',
          trigger: 'blur',
        }]">
            <el-cascader v-model="state.formInline.address_before" :options="options" placeholder="请选择" />
            <el-input v-model="state.formInline.address_after" placeholder="请输入详细地址" style="margin-top:10px;" clearable></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="info" @click="onCancel">取消</el-button>
            <el-button type="primary" @click="onSave">保存</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
import {ElMessage} from 'element-plus';
import { reactive, ref } from 'vue';
export default {
  name: 'addUpdate',
  setup(props, content) {
    const ruleForm = ref();
    const value = ref([]);
    let index = ref();
    let state = reactive({
      formInline: {
        name: '',
        age: '',
        sex: '',
        phone: '',
        address_before: '',
        address_after: ''
      }
    });
    const options = [
      {
        value: '北京市',
        label: '北京市',
        children: [
          {
            value: '海淀区',
            label: '海淀区'
          },
          {
            value: '丰台区',
            label: '丰台区'
          }
        ]
      },
      {
        value: '广东省',
        label: '广东省',
        children: [
          {
            value: '广州市',
            label: '广州市',
            children: [
              {
                label: '增城区',
                value: '增城区'
              },
              {
                label: '花都区',
                value: '花都区'
              },
              {
                label: '天河区',
                value: '天河区'
              }
            ]
          }
        ]
      }
    ];

    const onCancel = () => {
      content.emit('cancel', false);
    };
    const onSave = () => {
      ruleForm.value.validate((valid) => {
        console.log(valid);
        if (valid) {
          state.formInline.address_before = Object.values(
            state.formInline.address_before
          ).join('');
          let form = state.formInline;
          content.emit('addOrUpdateDate', { isShow: false, form, index });
          state.formInline = {
            name: '',
            age: '',
            sex: '',
            phone: '',
            address_before: '',
            address_after: ''
          };
        } else {
          ElMessage.info('请完整填写');
        }
      });
    };
    const getUserInfo = (val) => {
      state.formInline.name = val.data.name;
      state.formInline.age = val.data.age;
      state.formInline.sex = val.data.sex;
      state.formInline.phone = val.data.phone;
      state.formInline.address_after = val.data.address_after;
      let arr = val.data.address_before.split('');
      let a = arr.slice(0, 3).join('');
      let b = arr.slice(3, 6).join('');
      state.formInline.address_before = [a, b];
      if (arr.length > 6) {
        let c = arr.slice(6, 9).join('');
        state.formInline.address_before = [a, b, c];
      }
      index = val.index;
    };

    content.expose({
      getUserInfo
    });

    return {
      ruleForm,
      value,
      index,
      options,
      state,
      onCancel,
      onSave
    };
  }
};
</script>

<style scoped lang="less">
.mask {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.6);
  .content {
    width: 600px;
    height: 450px;
    background-color: #fff;
    margin: 250px auto;
    box-sizing: border-box;
    padding: 20px 0;
    .top {
      width: 100%;
      text-align: center;
    }
    .middle {
      margin: 20px 0 0 60px;
    }
  }
}
</style>