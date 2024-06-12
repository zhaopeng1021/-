<template>
  <div class="container">
    <!-- 顶部输入框和保存按钮 -->
    <el-form :inline="true" class="form-container">
      <el-form-item label="评审批次">
        <el-input v-model="reviewBatch" placeholder="请输入评审批次"></el-input>
      </el-form-item>
      <el-form-item label="评审日期">
        <el-date-picker v-model="reviewStartDate" type="datetime" value-format="YYYY-MM-DD HH:mm:ss" placeholder="选择日期"></el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-date-picker v-model="reviewEndDate" type="datetime" value-format="YYYY-MM-DD HH:mm:ss" placeholder="选择日期"></el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-icon><Select @click="handleSave" /></el-icon>
      </el-form-item>
    </el-form>

    <!-- 主表格 -->
    <el-table :data="mainTableData" style="width: 100%" @row-click="handleMainTableRowClick" class="main-table">
      <el-table-column prop="id" label="序号" width="150"></el-table-column>
      <el-table-column prop="batch" label="评审批次" width="220">
        <template #default="scope">
          <span class="link" @click="handleBatchClick(scope.row)">{{ scope.row.batch }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="startDate" label="评审开始" width="240"></el-table-column>
      <el-table-column prop="endDate" label="评审结束" width="240"></el-table-column>
      <el-table-column label="评审员" width="220">
        <template #default="scope">
          <span>{{ scope.row.reviewer.name }}</span>
<!--          <el-button type="text" icon="el-icon-edit" @click="handleReviewerEdit(scope.row)"></el-button>-->
          <el-icon @click="handleReviewerEdit(scope.row)" class="reviewer-icon"><Edit /></el-icon>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="200">
        <template #default="scope">
          <el-icon @click="handleEdit(scope.row)" type="primary"><Edit /></el-icon>
          <el-icon @click="handleDelete(scope.row)" type="danger"><DeleteFilled /></el-icon>
        </template>
      </el-table-column>
    </el-table>

    <!-- 子表格 -->
    <div v-if="selectedBatch" class="sub-table-container">
      <div class="sub-table-header">
        <div>
          <h3>{{ selectedBatch }} 评审课题</h3>
          <el-icon><UploadFilled @click="handleExport" class="export-button" /></el-icon>
        </div>
      </div>
      <el-table :data="subTableData" style="width: 100%" class="sub-table">
        <el-table-column prop="id" label="序号" width="180"></el-table-column>
        <el-table-column prop="topic" label="评审课题" width="400"></el-table-column>
        <el-table-column prop="leader" label="课题负责人" width="300"></el-table-column>
        <el-table-column label="操作" width="300">
          <template #default="scope">
            <el-icon><User @click="handleAssignLeader(scope.row)" /></el-icon>
            <el-icon @click="handleSubTableEdit(scope.row)" type="primary"><Edit /></el-icon>
            <el-icon @click="handleSubTableDelete(scope.row)" type="danger"><DeleteFilled /></el-icon>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { ElMessageBox, ElMessage } from 'element-plus';

const reviewBatch = ref('');
const reviewStartDate = ref('');
const reviewEndDate = ref('');

const mainTableData = ref([
  { id: 1, batch: '第一批次', startDate: '2023-06-01 08:00:00', endDate: '2023-06-10 17:00:00', reviewer: { name: '张三' } },
  { id: 2, batch: '第二批次', startDate: '2023-06-15 09:00:00', endDate: '2023-06-25 18:00:00', reviewer: { name: '李四' } },
  // 更多数据...
]);

const subTableData = ref([]);
const selectedBatch = ref('');

const handleSave = () => {
  ElMessage.success('保存成功');
};

const handleMainTableRowClick = (row) => {
  // 主表格行点击处理
};

const handleBatchClick = (row) => {
  selectedBatch.value = row.batch;
  subTableData.value = [
    { id: 1, topic: '课题1', leader: '王五' },
    { id: 2, topic: '课题2', leader: '赵六' },
    // 更多数据...
  ];
};

const handleReviewerEdit = (row) => {
  // 编辑评审员处理
};

const handleEdit = (row) => {
  // 编辑处理
};

const handleDelete = (row) => {
  ElMessageBox.confirm(
      `此操作将永久删除评审 ${row.batch}, 是否继续?`,
      '提示',
      {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
      }
  ).then(() => {
    const index = mainTableData.value.findIndex((item) => item.id === row.id);
    if (index !== -1) {
      mainTableData.value.splice(index, 1);
      ElMessage.success('删除成功!');
    }
  }).catch(() => {
    ElMessage.info('已取消删除');
  });
};

const handleExport = () => {
  ElMessage.success('导出成功');
};

const handleAssignLeader = (row) => {
  // 指定负责人处理
};

const handleSubTableEdit = (row) => {
  // 子表格编辑处理
};

const handleSubTableDelete = (row) => {
  ElMessageBox.confirm(
      `此操作将永久删除课题 ${row.topic}, 是否继续?`,
      '提示',
      {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
      }
  ).then(() => {
    const index = subTableData.value.findIndex((item) => item.id === row.id);
    if (index !== -1) {
      subTableData.value.splice(index, 1);
      ElMessage.success('删除成功!');
    }
  }).catch(() => {
    ElMessage.info('已取消删除');
  });
};
</script>

<style scoped>
.container {
  width: 100%;
  padding: 20px;
}

.form-container {
  margin-bottom: 20px;
}

.main-table {
  width: calc(100% - 40px); /* 让表格宽度减去左右边距 */
  margin: 0 auto;
}

.link {
  color: blue;
  cursor: pointer;
}

.reviewer-icon {
  margin-left: 5px;
  font-size: 15px;
  cursor: pointer;
}

.sub-table-container {
  margin-top: 20px;
  padding: 20px;
  background: #fff;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);
  border-radius: 4px;
}

.sub-table-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.sub-table-header h3 {
  margin: 0;
  display: inline;
  margin-right: 20px;
}

.export-button {
  font-size: 24px;
  cursor: pointer;
}

.el-button {
  margin-bottom: 20px;
}

.dialog-footer {
  text-align: right;
}

.sub-table {
  width: calc(100% - 40px); /* 让子表格宽度减去左右边距 */
  margin: 0 auto;
}
</style>
