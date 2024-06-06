<template>
  <div>
    <form @submit.prevent="handleSubmit">
      茶叶ID:<input v-model="form.id" type="text" name="id" />
      茶叶名称:<input v-model="form.name" type="text" name="name" />
      茶叶类型:<input v-model="form.type" type="text" name="type" />
      茶叶价格:<input v-model="form.price" type="text" name="price" />
      茶叶库存:<input v-model="form.stock" type="text" name="stock" />
      茶叶描述:<input v-model="form.description" type="text" name="description" />
      <input type="submit" value="提交" />
    </form>
    <button>搜索</button>
    <br><br>
    <table border="1">
      <thead>
        <tr align="center">
          <th style="width: 100px;">茶叶ID</th>
          <th style="width: 100px;">茶叶名称</th>
          <th style="width: 100px;">茶叶类型</th>
          <th style="width: 100px;">茶叶价格</th>
          <th style="width: 100px;">茶叶库存</th>
          <th style="width: 100px;">茶叶描述</th>
          <th style="width: 100px;">创建时间</th>
          <th style="width: 100px;">更新时间</th>
          <th style="width: 100px;">删除</th>
          <th style="width: 100px;">修改</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in teas" :key="item.id" align="center">
          <td>{{ item.id }}</td>
          <td>{{ item.name }}</td>
          <td>{{ item.type }}</td>
          <td>{{ item.price }}</td>
          <td>{{ item.stock }}</td>
          <td>{{ item.description }}</td>
          <td>{{ formatDate(item.created_at) }}</td>
          <td>{{ formatDate(item.updated_at) }}</td>
          <td><button @click="deleteTea(item.id)">删除</button></td>
          <td><button @click="editTea(item)">修改</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      form: {
        id: '',
        name: '',
        type: '',
        price: '',
        stock: '',
        description: ''
      },
      teas: [],
      updateId: null
    };
  },
  created() {
    this.fetchTeas();
    setInterval(this.fetchTeas, 60000);
  },
  methods: {
    fetchTeas() {
      axios.get('https://liu.zzgoodqc.cn/teas')
        .then(response => {
          this.teas = response.data.data;
        })
        .catch(error => {
          console.error(error);
        });
    },
    handleSubmit() {
      if (this.updateId) {
        this.updateTea();
      } else {
        this.addTea();
      }
    },
    addTea() {
      axios.post('https://liu.zzgoodqc.cn/teas', this.form)
        .then(response => {
          this.fetchTeas();
          this.clearForm();
        })
        .catch(error => {
          console.error(error);
        });
    },
    editTea(tea) {
      this.form = { ...tea };
      this.updateId = tea.id;
    },
    updateTea() {
      axios.put(`https://liu.zzgoodqc.cn/teas/${this.updateId}`, this.form)
        .then(response => {
          this.fetchTeas();
          this.clearForm();
          this.updateId = null;
        })
        .catch(error => {
          console.error(error);
        });
    },
    deleteTea(id) {
      axios.delete(`https://liu.zzgoodqc.cn/teas/${id}`)
        .then(response => {
          this.fetchTeas();
        })
        .catch(error => {
          console.error(error);
        });
    },
    formatDate(dateStr) {
      const date = new Date(dateStr);
      return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`;
    },
    clearForm() {
      this.form = {
        id: '',
        name: '',
        type: '',
        price: '',
        stock: '',
        description: ''
      };
    }
  }
};
</script>

<style scoped>
table {
  border-collapse: collapse;
  width: 100%;
}

th,
td {
  padding: 10px;
  text-align: center;
}

th {
  background-color: #4bf4d8;
}

tr:nth-child(2n) {
  background-color: #3dd3f5;
}

input[type="text"] {
  padding: 5px;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid #51f4be;
  margin-bottom: 10px;
}

input[type="submit"],
button {
  width: 66px;
  height: 30px;
  border: none;
  background-color: #181819;
  color: #eb8b32;
}

input {
  width: 100px;
}

input[type="checkbox"] {
  width: 25px;
  height: 25px;
}
</style>
