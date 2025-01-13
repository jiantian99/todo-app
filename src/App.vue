<template>
  <div class="todo-container">
    <h1>{{ currentDate }}</h1>
    <div class="input-container">
      <el-tag
        :type="getCurrentPriorityType"
        effect="dark"
        class="priority-tag"
        @click="cyclePriority"
      >
        <el-icon><Flag /></el-icon>
      </el-tag>
      <el-input
        v-model="newTodo"
        placeholder="请输入新的待办事项"
        @keyup.enter="addTodo"
        class="todo-input"
      />
      <el-button type="primary" @click="addTodo" class="add-button">添加</el-button>
      <el-button type="danger" @click="confirmClearAll" :disabled="!todos.length">
        一键清空
      </el-button>
    </div>

    <el-divider />

    <el-table :data="sortedTodos" style="width: 100%">
      <el-table-column label="状态" width="80">
        <template #default="scope">
          <el-checkbox v-model="scope.row.done" @change="saveTodos" />
        </template>
      </el-table-column>
      <el-table-column label="优先级" width="120">
        <template #default="scope">
          <el-tag
            :type="
              scope.row.priority === 'normal'
                ? 'success'
                : scope.row.priority === 'urgent'
                ? 'warning'
                : 'danger'
            "
          >
            <el-icon><Flag /></el-icon>
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="事项">
        <template #default="scope">
          <span
            :class="{
              'done-item': scope.row.done,
              'normal-priority': scope.row.priority === 'normal',
              'urgent-priority': scope.row.priority === 'urgent',
              'critical-priority': scope.row.priority === 'critical',
            }"
          >
            {{ scope.row.text }}
          </span>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="120">
        <template #default="scope">
          <el-button
            type="danger"
            size="small"
            @click="removeTodo(scope.$index)"
          >
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { Flag } from '@element-plus/icons-vue'

export default {
  name: 'App',
  components: {
    Flag,
  },
  data() {
    return {
      newTodo: '',
      newPriority: 'normal',
      todos: [],
      currentDate: this.formatDate(),
    };
  },
  computed: {
    sortedTodos() {
      return [...this.todos].sort((a, b) => {
        if (a.done !== b.done) {
          return a.done ? 1 : -1;
        }
        if (!a.done) {
          const priorityOrder = { critical: 3, urgent: 2, normal: 1 };
          return priorityOrder[b.priority] - priorityOrder[a.priority];
        }
        return 0;
      });
    },
    getCurrentPriorityType() {
      const priorityMap = Object.freeze({
        normal: 'success',
        urgent: 'warning',
        critical: 'danger'
      });
      return priorityMap[this.newPriority];
    }
  },
  methods: {
    formatDate() {
      return new Date().toLocaleDateString('zh-CN', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        weekday: 'long'
      });
    },
    addTodo() {
      const trimmedTodo = this.newTodo.trim();
      if (!trimmedTodo) {
        this.$message.warning('请输入待办事项');
        return;
      }
      const newTodo = {
        text: trimmedTodo,
        priority: this.newPriority,
        done: false,
      };
      this.todos.push(newTodo);
      this.newTodo = '';
      this.newPriority = 'normal';
      this.saveTodos();
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
      this.saveTodos();
    },
    saveTodos() {
      try {
        localStorage.setItem('todos', JSON.stringify(this.todos));
      } catch (e) {
        console.error('保存待办事项失败:', e);
        this.$message.error('保存失败，请检查浏览器存储空间');
      }
    },
    loadTodos() {
      try {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
          this.todos = JSON.parse(savedTodos);
        }
      } catch (e) {
        console.error('加载待办事项失败:', e);
        this.todos = [];
      }
    },
    cyclePriority() {
      const priorityOrder = Object.freeze(['normal', 'urgent', 'critical']);
      const currentIndex = priorityOrder.indexOf(this.newPriority);
      this.newPriority = priorityOrder[(currentIndex + 1) % priorityOrder.length];
    },
    confirmClearAll() {
      this.$confirm('确认清空所有待办事项吗？此操作不可恢复', '警告', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.clearAll();
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消清空操作'
        });
      });
    },
    clearAll() {
      this.todos = [];
      this.saveTodos();
      this.$message({
        type: 'success',
        message: '已清空所有待办事项'
      });
    },
  },
  mounted() {
    this.loadTodos();
  }
};
</script>

<style>
.todo-container {
  max-width: 800px;
  margin: 50px auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  margin-bottom: 20px;
  color: #303133;
}

.input-container {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  width: 100%;
  align-items: center;
}

.todo-input {
  flex: 1;
}

.add-button.el-button {
  width: 60px !important;
}

.done-item {
  text-decoration: line-through;
  color: #999;
}

.normal-priority {
  color: #67c23a;
}

.urgent-priority {
  color: #e6a23c;
}

.critical-priority {
  color: #f56c6c;
}

.priority-tag {
  cursor: pointer;
  user-select: none;
  height: 32px !important;
  line-height: 32px !important;
  padding: 0 12px;
  font-size: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.priority-tag .el-icon {
  font-size: 16px;
  margin: 0;
}

.priority-tag:hover {
  opacity: 0.8;
}
</style>