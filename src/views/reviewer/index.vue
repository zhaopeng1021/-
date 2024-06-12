<template>
  <div class="container">
    <div class="button-container">
      <el-button type="primary" icon="el-icon-plus" @click="dialogFormVisible = true">新增</el-button>
    </div>
    <div class="reviewer-table-container">
      <el-table :data="reviewers" style="width: 100%">
        <el-table-column prop="id" label="序号" width="100"></el-table-column>
        <el-table-column prop="name" label="姓名" width="180"></el-table-column>
        <el-table-column prop="phone" label="手机号码" width="180"></el-table-column>
        <el-table-column prop="organization" label="所在单位" width="220"></el-table-column>
        <el-table-column prop="title" label="职称" width="180"></el-table-column>
        <el-table-column label="操作" width="150">
          <template #default="scope">
            <el-icon @click="handleEdit(scope.row)"  type="primary"><Edit /></el-icon>
                        <el-icon @click="handleDelete(scope.row)" type="danger"><DeleteFilled /></el-icon>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <el-dialog v-model="dialogFormVisible" :title="dialogTitle" width="500">
      <el-form :model="form" :label-width="formLabelWidth">
        <el-form-item label="姓名">
          <el-input v-model="form.name" autocomplete="off" />
        </el-form-item>
        <el-form-item label="手机号码">
          <el-input v-model="form.phone" autocomplete="off" />
        </el-form-item>
        <el-form-item label="所在单位">
          <el-input v-model="form.organization" autocomplete="off" />
        </el-form-item>
        <el-form-item label="职称">
          <el-select v-model="form.title" placeholder="请选择">
            <el-option label="教授" value="教授"></el-option>
            <el-option label="副教授" value="副教授"></el-option>
            <el-option label="讲师" value="讲师"></el-option>
            <el-option label="其他" value="其他"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item v-if="form.title === '其他'" label="其他职称">
          <el-input v-model="form.otherTitle" autocomplete="off" />
        </el-form-item>
      </el-form>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取消</el-button>
          <el-button type="primary" @click="submitForm">确定</el-button>
        </div>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue';
import { ElMessageBox, ElMessage } from 'element-plus';

const reviewers = ref([
  { id: 1, name: '张三', phone: '13800000000', organization: '某单位', title: '教授' },
  { id: 2, name: '李四', phone: '13900000000', organization: '某单位', title: '副教授' },
  // 更多数据...
]);

const dialogFormVisible = ref(false);
const dialogTitle = ref('新增评审员');
const formLabelWidth = '100px';
const form = reactive({
  id: null,
  name: '',
  phone: '',
  organization: '',
  title: '',
  otherTitle: '',
});

const handleAdd = () => {
  dialogTitle.value = '新增评审员';
  form.id = null;
  form.name = '';
  form.phone = '';
  form.organization = '';
  form.title = '';
  form.otherTitle = '';
  dialogFormVisible.value = true;
};

const handleEdit = (row) => {
  dialogTitle.value = '编辑评审员';
  form.id = row.id;
  form.name = row.name;
  form.phone = row.phone;
  form.organization = row.organization;
  form.title = row.title;
  form.otherTitle = row.title === '其他' ? row.otherTitle : '';
  dialogFormVisible.value = true;
};

const handleDelete = (row) => {
  ElMessageBox.confirm(
      `此操作将永久删除评审员 ${row.name}, 是否继续?`,
      '提示',
      {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
      }
  ).then(() => {
    const index = reviewers.value.findIndex((reviewer) => reviewer.id === row.id);
    if (index !== -1) {
      reviewers.value.splice(index, 1);
      ElMessage({
        type: 'success',
        message: '删除成功!',
      });
    }
  }).catch(() => {
    ElMessage({
      type: 'info',
      message: '已取消删除',
    });
  });
};

const submitForm = () => {
  if (!form.name || !form.phone || !form.organization || !form.title) {
    ElMessage.error('请填写完整的信息');
    return;
  }
  if (form.id === null) {
    const newId = reviewers.value.length ? reviewers.value[reviewers.value.length - 1].id + 1 : 1;
    reviewers.value.push({ ...form, id: newId });
  } else {
    const index = reviewers.value.findIndex((reviewer) => reviewer.id === form.id);
    if (index !== -1) {
      reviewers.value[index] = { ...form };
    }
  }
  dialogFormVisible.value = false;
};
</script>

<style scoped>
.container {
  width: 100%;
  padding: 20px;
}

.button-container {
  margin-bottom: 20px;
  text-align: right;
}

.reviewer-table-container {
  width: 100%;
  background: #fff;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);
  border-radius: 4px;
  padding: 20px;
}

.el-button {
  margin-bottom: 20px;
}

.dialog-footer {
  text-align: right;
}
</style>
