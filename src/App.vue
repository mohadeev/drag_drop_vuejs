<script setup>
import { ref, onMounted } from "vue";
import { v4 as uuid } from "uuid";
const listsName = ref("hee");
const textdDef = ref("");
const lists = ref([
  {
    id: 1,
    title: "List 1",
  },
  {
    id: 2,
    title: "List 2",
  },
]);
const items = ref([
  {
    id: 13123,
    content: "Item 1",
    list: 1,
    editing: false,
  },
  {
    id: 13145,
    content: "Item 2",
    list: 2,
    editing: false,
  },
]);

const createLocalStorge = (newLists, newItems) => {
  localStorage.setItem("lists", JSON.stringify(newLists));
  localStorage.setItem("items", JSON.stringify(newItems));
};

const GetListItems = (listId) => {
  return items.value.filter((item) => item.list === listId);
};

const DragStart = (evt, itemID) => {
  evt.dataTransfer.setData("itemID", itemID);
  evt.dataTransfer.effectAllowed = "move";
  evt.dataTransfer.dropEffect = "move";
  createLocalStorge(lists.value, items.value);
};

const DropItem = (evt, listID) => {
  const itemID = evt.dataTransfer.getData("itemID");
  const item = items.value.find((item) => item.id == itemID);
  item.list = listID;
  createLocalStorge(lists.value, items.value);
};

const CreateNewItem = (evt, listID) => {
  evt.stopPropagation();

  items.value.push({
    id: uuid(),
    content: "New Item",
    list: listID,
    editing: true,
  });
  createLocalStorge(lists.value, items.value);
};

const EditItem = (evt, itemID) => {
  evt.stopPropagation();
  const item = items.value.find((item) => item.id == itemID);
  item.editing = true;
  createLocalStorge(lists.value, items.value);
};
const removeEditItem = (evt, itemID) => {
  const item = items.value.findIndex((item) => item.id == itemID);
  items.value.splice(item, 1);
  createLocalStorge(lists.value, items.value);
};
const removeEditList = (evt, itemID) => {
  const item = lists.value.findIndex((item) => item.id == itemID);
  lists.value.splice(item, 1);
  createLocalStorge(lists.value, items.value);
};
const CreateNewList = () => {
  const value = textdDef.value;
  lists.value.push({
    id: uuid(),
    title: value,
  });
  textdDef.value = "";
  createLocalStorge(lists.value, items.value);
};
const changeName = (e, itemID) => {
  //   alert("sldñ");
  const item = items.value.findIndex((item) => item.id == itemID);
  items.value[item].content = e.target.value;
  createLocalStorge(lists.value, items.value);
};

onMounted(() => {
  const newlocalStorageList = localStorage.getItem("lists");
  if (newlocalStorageList !== null) {
    const newParedStroage = JSON.parse(newlocalStorageList);
    lists.value = newParedStroage;
  }
  const newlocalStorageItems = localStorage.getItem("items");
  if (newlocalStorageList !== null) {
    const newParedStroageItems = JSON.parse(newlocalStorageItems);
    items.value = newParedStroageItems;
  }
});

const elementValue = ref("Initial value");
const updateElementValue = () => {
  elementValue.value = "New value";
};
</script>

<template>
  <main>
    <div class="div_top_control">
      <h1>Todo List</h1>
      <div class="top_items">
        <input
          class="input_top"
          v-model="textdDef"
          @keyDown="addTodo"
          maxlength="100"
        />
        <button @click="CreateNewList">Create List</button>
      </div>
    </div>

    <div class="lists">
      <div v-for="list in lists" class="list">
        <div class="div_top">
          <h2>{{ list.title }}</h2>
          <div class="main_div_top_right_row">
            <button
              @click="CreateNewItem($event, list.id)"
              class="button_submite"
            >
              +
            </button>
            <button
              class="button_submite_remove"
              @click="removeEditList($event, list.id)"
            >
              -
            </button>
          </div>
        </div>
        <div
          class="items"
          @dragover.prevent
          @drop.prevent="DropItem($event, list.id)"
        >
          <div
            v-for="item in GetListItems(list.id)"
            class="item"
            draggable="true"
            @dragstart="DragStart($event, item.id)"
            @dblclick="EditItem($event, item.id)"
          >
            <input
              type="text"
              v-model="item.content"
              @input="changeName($event, item.id)"
              @blur="item.editing = false"
              class="text_input"
            />
            <button @click="removeEditItem($event, item.id)">remove</button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "montserrat", sans-serif;
  user-select: none;
}

body {
  background-color: #282947;
  color: white;
}
.top_items {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  column-gap: 10px;
}

main {
  padding: 1.5rem;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

h1 {
}

button {
  appearance: none;
  border: none;
  outline: none;
  background-color: crimson;
  color: white;
  padding: 0.5rem;
  border-radius: 0.25rem;
  cursor: pointer;
}

.lists {
  flex: 1 1 0%;
  display: flex;
  align-items: flex-start;
  width: 100%;
  margin: 0;
  flex-direction: row;
  row-gap: 5px;
  flex-wrap: wrap;
}

.list {
  margin: 0 1rem;
  min-width: 250px;
  background-color: rgba(255, 255, 255, 0.1);
  padding: 1rem;
  border-radius: 0.5rem;
  box-sizing: initial;
  display: flex;
  flex-direction: column;
  align-items: center;
  row-gap: 10px;
}
.list .items {
  width: 100%;
  min-height: 60px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  row-gap: 5px;
}

.items .item {
  padding: 0.5rem;
  background-color: #eee;
  color: #282947;
  border-radius: 10px;
}

.items .item input {
  color: #282947;
  appearance: none;
  border: none;
  outline: none;
  background: none;
}
.div_top {
  width: 100%;
  height: auto;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
}
.button_submite,
.button_submite_remove {
  background-color: #fff;
  border: 2px solid #000;
  color: #000;
}
.button_submite_remove {
  border: 2px solid #000;
}
.main_div_top_right_row {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-end;
  column-gap: 10px;
  width: auto;
  height: auto;
}
.div_top_control {
  width: auto;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-start;
  column-gap: 100px;
  margin-bottom: 50px;
}
.input_top {
  min-width: 100px;
  height: 35px;
  border: 2px solid #000;
  outline: none;
  border-radius: 10px;
}
.text_input {
  background-color: red;
}
</style>
