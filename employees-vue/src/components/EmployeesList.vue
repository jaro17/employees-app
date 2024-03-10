<script setup>
import {onMounted, ref} from "vue";
import axios from "axios";

let employees = ref([])
let fullname = ref("")
let department = ref("")
let birthdate = ref("")
let salary = ref("")
let id = ref("")
let isEditing = ref(false)

const getEmployees = async () => {
  try {
    const response = await axios.get("http://localhost:8000/api/employees")
    employees.value = response.data
  } catch (error) {
    console.log(error)
  }
}

onMounted(() => {
  getEmployees();
})

const submitEmployee = async () => {
  try {
      if (!isEditing.value) {
        await this.$http
          .post(`http://127.0.0.1:8000/api/employees/`, {
            fullname: this.fullname,
            dep: this.dep,
            birthdate: this.birthdate,
            salary: this.salary,
          })
          .then((response) => {
            employees.value.push(response.data);
            this.fullname = "";
            this.dep = "";
            this.birthdate = "";
            this.salary = "";
          });
      } else {
        await this.$http
          .put(`http://127.0.0.1:8000/api/employees/${this.id}/`, {
            fullname: this.fullname,
            dep: this.dep,
            birthdate: this.birthdate,
            salary: this.salary,
          })
          .then((response) => {
            let objIndex = employees.value.findIndex((s) => s.id === id.value);
            let tmp = employees.value;
            tmp[objIndex] = response.data;
            this.employees = tmp;
            this.fullname = "";
            this.dep = "";
            this.birthdate = "";
            this.salary = "";
            this.id = "";
            this.isEditing = false;
            getEmployees();
          });
      }
    } catch (error) {
      console.log(error);
    }
};

const editEmployee = async (employee) => {
   try {
    isEditing.value = true;
    fullname.value = employee.fullname;
    department.value = employee.dep;
    birthdate.value = employee.birthdate;
    salary.value = employee.salary;
    id.value = employee.id;
  } catch (error) {
    console.log(error);
  }
};

const deleteEmployee = async (employee) => {
 if (!confirm("Are you sure?")) {
    return;
  }
  try {
    await axios.delete(`http://127.0.0.1:8000/api/employees/${employee.id}/`);
    await getEmployees();
  } catch (error) {
    console.error(error);
  }
}
</script>

<template>
 <div class="content">
    <h1>Employees App</h1>

     <form v-on:submit.prevent="submitEmployee" class="form-group">
       <div class="form-group">
         <input
           type="text"
           class="form-control form-input"
           placeholder="Full Name"
           v-model="fullname"
       />
         <input
             type="text"
             class="form-control form-input"
             placeholder="Birth Date"
             v-model="dep"
         />
         <input
             type="text"
             class="form-control form-input"
             placeholder="Department"
             v-model="birthdate"
         />
         <input
             type="text"
             class="form-control form-input"
             placeholder="Salary"
             v-model="salary"
         />
         <button type="submit" class="btn btn-primary">
           {{ isEditing ? "Edit Employee" : "Add Employee" }}
         </button>
       </div>
      </form>

    <div class="list">
      <ul class="list_content">
        <li v-for="employee in employees" :key="employee.id">
          <div class="card">
            <div class="card-header">
              <h4>{{ employee.fullname }}</h4>
            </div>
            <div class="card-body">
              <h4>{{ employee.dep }}</h4>
              <h4>{{ employee.birthdate }}</h4>
              <h4>{{ employee.salary }}</h4>
            </div>
            <div class="card-footer">
            <button class="btn" @click="editEmployee(employee)">Edit</button>
            <button class="btn btn-error" @click="deleteEmployee(employee)">Delete</button>
          </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
h1 {
  text-align: center;
  margin-top: 2rem;
}

form {
  max-width: 50%;
  margin: 0 auto;
}

.form-input {
  margin-bottom: 0.5rem;
}

.list {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.list_content li {
  list-style-type: none;
}

.card {
  width: 22rem;
  box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1);
  border-radius: 0.5rem;
}

.card-footer button:first-child {
  margin-right: 1rem;
}


</style>