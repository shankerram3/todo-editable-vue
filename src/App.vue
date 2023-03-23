
<script setup>
import { ref } from "vue";

const newTask = ref("");
const incompleteTasks = ref([]);
const completeTasks = ref([]);
const editable = ref(false);
const showModal = ref(false);
const editIndex = ref(null);
const isChecked = ref(null)


const addTasks = () => {
  if (newTask.value.length <= 9) {
    alert("character length must be greater than 9");
    return;
  } else {
    if (editIndex.value === null) {
      incompleteTasks.value.push({
        text: newTask.value,
        isChecked: false,
      });
      newTask.value = "";
    } else {
      incompleteTasks.value[editIndex.value].text = newTask.value;
      newTask.value = "";
      editIndex.value = null;
      showModal.value = false;
    }
  }
};
function handleCheckuncompleted(index) {
  isChecked.value = incompleteTasks.value[index].isChecked.value
  incompleteTasks.value[index].isChecked = !isChecked.value
  console.log(incompleteTasks.value[index].isChecked);
  isChecked.value= null
}
function handleCheckcompleted(index) {
  isChecked.value = completeTasks.value[index].isChecked.value
  completeTasks.value[index].isChecked = isChecked.value
  console.log(completeTasks.value[index].isChecked);
  isChecked.value= null
}
function addToCompletedList(index) {
    completeTasks.value.push({
      text: incompleteTasks.value[index].text,
      isChecked: incompleteTasks.value[index].isChecked,
    });
    incompleteTasks.value.splice(index, 1);
}
function addToUncompletedList(index){
  incompleteTasks.value.push({
      text: completeTasks.value[index].text,
      isChecked: completeTasks.value[index].isChecked,
    });
    completeTasks.value.splice(index, 1);
}
function deleteTaskUncompleted(index) {
  incompleteTasks.value.splice(index, 1);
}
function deleteTaskCompleted(index) {
  completeTasks.value.splice(index, 1);
}
function editTask(index) {
  showModal.value = true;
  editIndex.value = index;
}
</script>


<template>
  <body>
    <div v-show="showModal" class="overlay">
      <div class="modal">
        <textarea
          v-model="newTask"
          name="edited-note"
          id="note"
          cols="10"
          rows="5"
        ></textarea>
        <button @click="addTasks">Add Note</button>
        <button class="close" @click="showModal = false">Close</button>
      </div>
    </div>
    <div class="container">
      <h2>TODO LIST</h2>
      <h3>Add Item</h3>
      <p>
        <input v-model="newTask" id="new-task" type="text" />{{ newTask.text
        }}<button @click="addTasks">Add</button>
      </p>

      <h3>Todo</h3>

      <ul
        v-for="(task, index) in incompleteTasks"
        :key="index"
        :id="task.isChecked ? 'completed-tasks' : 'incomplete-tasks'"
      >
        <li>
          <input
            type="checkbox"
            v-model="task.isChecked"
            @change="handleCheckuncompleted(index), addToCompletedList(index)"
            :checked="task.isChecked"
          />
          <label v-if="editable != true">{{ task.text }}</label>
          <p>
            <input
              v-if="editable"
              v-model="newTask"
              id="new-task"
              type="text"
            />{{ newTask.text }}
          </p>
          <button @click="editTask(index)" class="edit">Edit</button>
          <button @click="deleteTaskUncompleted(index)" class="delete">Delete</button>
        </li>
      </ul>

      <h3>Completed</h3>
      <ul
        v-for="(task, index) in completeTasks"
        :key="index"
        :id="task.isChecked ? 'completed-tasks' : 'incomplete-tasks'"
      >
        <li>
          <input
            type="checkbox"
            v-model="task.isChecked"
            @change="handleCheckcompleted(index), addToUncompletedList(index)"
            :checked="task.isChecked"
          />
          <label v-if="editable != true">{{ task.text }}</label>
          <p>
            <input
              v-if="editable"
              v-model="newTask"
              id="new-task"
              type="text"
            />{{ newTask.text }}
          </p>
          <button @click="deleteTaskCompleted(index, task.isChecked)" class="delete">
            Delete
          </button>
        </li>
      </ul>
    </div>
  </body>
</template>
<style >
body {
  background: #fff;
  height: 100vh;
  margin: 0;
  color: #333;
  font-family: Lato, sans-serif;
  background-color: #e4e4e4;
}

.container {
  display: block;
  width: 400px;
  margin: 100px auto auto auto;
  background-color: #fff;
  padding: 0px 10px 10px 10px;
  border-radius: 10px;
}

h2 {
  text-align: center;
  padding-top: 10px;
  margin-bottom: 0px;
}

ul {
  margin: 0;
  padding: 0;
}

li * {
  float: left;
}

li,
h3 {
  clear: both;
  list-style: none;
}

input,
button {
  outline: none;
}

button {
  background: none;
  border: 0px;
  color: #888;
  font-size: 15px;
  width: 60px;
  margin: 10px 0 0;
  font-family: Lato, sans-serif;
  cursor: pointer;
}

button:hover {
  color: #333;
}

/* Heading */

h3,
label[for="new-task"] {
  color: #333;
  font-weight: 700;
  font-size: 15px;
  border-bottom: 2px solid #333;
  padding: 20px 0 10px;
  margin: 0;
  text-transform: uppercase;
}

input[type="text"] {
  margin: 0;
  font-size: 18px;
  line-height: 18px;
  height: 18px;
  padding: 10px;
  border: 1px solid #ddd;
  background: #fff;
  border-radius: 6px;
  font-family: Lato, sans-serif;
  color: #888;
}

input[type="text"]:focus {
  color: #333;
}

/* New Task */

label[for="new-task"] {
  display: block;
  margin: 0 0 20px;
}

input#new-task {
  float: left;
  width: 318px;
}

p > button:hover {
  color: #0fc57c;
}

/* Task list */

li {
  overflow: hidden;
  padding: 20px 0;
  border-bottom: 1px solid #eee;
}

li > input[type="checkbox"] {
  margin: 0 10px;
  position: relative;
  top: 15px;
}

li > label {
  font-size: 18px;
  line-height: 40px;
  width: 237px;
  padding: 0 0 0 11px;
}

li > input[type="text"] {
  width: 226px;
}

li > .delete:hover {
  color: #cf2323;
}

/* Completed */

#completed-tasks label {
  text-decoration: line-through;
  color: #888;
}

/* Edit Task */

ul li input[type="text"] {
  display: none;
}

ul li.editMode input[type="text"] {
  display: block;
}

ul li.editMode label {
  display: none;
}
.modal {
  width: 750px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}
.modal button {
  padding: 10px 20px;
  font-size: 20px;
  width: 100%;
  background-color: #434141;
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 15px;
}
.modal p {
  margin-left: auto;
  font-size: 20px;
  z-index: 100000;
  cursor: pointer;
}
textarea {
  width: 100%;
  height: 200px;
  padding: 5px;
  font-size: 20px;
}
.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.77);
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
}
</style>