<template>
  <div class="todo__wrapper">
    <div class="todo__container">
      <h1 class="todo__title">TODO List</h1>
      <div class="todo__header">
        <label for="name">Название задачи</label>
        <input 
          v-model.trim="todoName" 
          v-on:keydown.enter="addTodoItem"
          name="name" 
          id="name"
          placeholder="Введите название..."
        />
        <button class="todo__add-btn btn" @click="addTodoItem">Добавить задачу</button>
        <!-- Выдаёт ошибку при попытке ввести пустое поле -->
        <div class="error" v-if="error">{{error}}</div>
      </div>
      <div class="todo__list">
        <!-- Отображение элементов из списка задач -->
        <div class="todo__item" v-for="(item, index) in todoItems" v-bind:key="index">
          <!-- Отображает текстовые задачи с состоянием -->
          <template v-if="item.edit === false">
            <span class="todo__item-text">{{item.name}}</span>
            <div class="todo__item-control">
              <button class="todo__edit-btn icon-btn" @click="editTodoItem(item)">
                <svg version="1.1" with="24px" height="24px" viewBox="0 0 64 64" xml:space="preserve" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><g><g id="Icon-Pencil" transform="translate(179.000000, 382.000000)"><path class="st0" d="M-168.2-328l3.7-14.9l22.7-22.7l11.2,11.2l-22.7,22.7L-168.2-328L-168.2-328z M-161.9-341.5     l-2.4,9.6l9.6-2.4l20.2-20.2l-7.2-7.2L-161.9-341.5L-161.9-341.5z" id="Fill-168"/><path class="st0" d="M-155.7-332.6c-1-3.9-4-6.9-7.9-7.9l0.7-2.8c4.9,1.2,8.7,5,9.9,9.9L-155.7-332.6" id="Fill-169"/><polyline class="st0" id="Fill-170" points="-156,-338.1 -158,-340.2 -138.1,-360.1 -136.1,-358.1 -156,-338.1    "/><path class="st0" d="M-166.2-330l4.4-1.1c-0.4-1.6-1.7-2.9-3.3-3.3L-166.2-330" id="Fill-171"/><path class="st0" d="M-129.5-355.5l-11.2-11.2l4.5-4.5l0.7,0.1c5.4,0.7,9.7,5,10.4,10.4l0.1,0.7L-129.5-355.5     L-129.5-355.5z M-136.6-366.7l7.2,7.2l1.4-1.4c-0.8-3.6-3.6-6.4-7.2-7.2L-136.6-366.7L-136.6-366.7z" id="Fill-172"/></g></g></svg>
              </button>
              <button class="todo__delete-btn icon-btn" @click="deleteTodoItem(item)">
                <svg version="1.1" with="24px" height="24px" viewBox="0 0 64 64" xml:space="preserve" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><g><g id="Icon-Trash" transform="translate(232.000000, 228.000000)"><polygon class="st0" id="Fill-6" points="-207.5,-205.1 -204.5,-205.1 -204.5,-181.1 -207.5,-181.1    "/><polygon class="st0" id="Fill-7" points="-201.5,-205.1 -198.5,-205.1 -198.5,-181.1 -201.5,-181.1    "/><polygon class="st0" id="Fill-8" points="-195.5,-205.1 -192.5,-205.1 -192.5,-181.1 -195.5,-181.1    "/><polygon class="st0" id="Fill-9" points="-219.5,-214.1 -180.5,-214.1 -180.5,-211.1 -219.5,-211.1    "/><path class="st0" d="M-192.6-212.6h-2.8v-3c0-0.9-0.7-1.6-1.6-1.6h-6c-0.9,0-1.6,0.7-1.6,1.6v3h-2.8v-3     c0-2.4,2-4.4,4.4-4.4h6c2.4,0,4.4,2,4.4,4.4V-212.6" id="Fill-10"/><path class="st0" d="M-191-172.1h-18c-2.4,0-4.5-2-4.7-4.4l-2.8-36l3-0.2l2.8,36c0.1,0.9,0.9,1.6,1.7,1.6h18     c0.9,0,1.7-0.8,1.7-1.6l2.8-36l3,0.2l-2.8,36C-186.5-174-188.6-172.1-191-172.1" id="Fill-11"/></g></g></svg>
              </button>
            </div>
            <div class="todo__item-state">
              <!-- Отображает разное состояние в зависимости от значения item.ready -->
              <input type="checkbox" :id="index" v-model="item.ready" />
              <label class="not-ready" :for="index" v-if="!item.ready">Не готово</label>
              <label class="ready" :for="index" v-else>Готово</label>
            </div>
          </template>
          <!-- Отображение инпутов во время редактирования -->
          <template v-else>
            <input v-model="item.name" autofocus />
            <div class="todo__edit-control">
              <button class="todo__save-btn icon-btn" @click="saveChanges(item, index)">
                <svg viewBox="0 0 32 32" with="24px" height="24px"  xmlns="http://www.w3.org/2000/svg"><title/><g data-name="Layer 49" id="Layer_49"><path d="M29,31H3a1,1,0,0,1-1-1V2A1,1,0,0,1,3,1H23a1,1,0,0,1,.71.29l6,6A1,1,0,0,1,30,8V30A1,1,0,0,1,29,31ZM4,29H28V8.41L22.59,3H4Z"/><path class="cls-1" d="M20,9H10A1,1,0,0,1,9,8V2a1,1,0,0,1,1-1H20a1,1,0,0,1,1,1V8A1,1,0,0,1,20,9ZM11,7h8V3H11Z"/><path class="cls-1" d="M23,25H9a1,1,0,0,1-1-1V14a1,1,0,0,1,1-1H23a1,1,0,0,1,1,1V24A1,1,0,0,1,23,25ZM10,23H22V15H10Z"/></g></svg>
              </button>
              <button class="todo__cancel-btn icon-btn" @click="cancelChanges(item)">
                <svg viewBox="0 0 32 32" with="24px" height="24px" xmlns="http://www.w3.org/2000/svg"><title/><g><line x1="7" x2="25" y1="7" y2="25"/><line x1="7" x2="25" y1="25" y2="7"/></g></svg>
              </button>
            </div>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>
<script>


export default {
  name: 'App',
  data() {
    return {
      todoName: '',
      todoItems: [],
      // beforeEdit: '',
      error: ''
    }
  },
  methods: {
    // Добавляет новую задачу в в массив todoItems c валидацией на пустое поле
    addTodoItem() {
      const newTodoItem = {name: this.todoName, ready: false, edit: false, beforeEdit: this.todoName};
      if(this.todoName) {
        this.todoItems.push(newTodoItem);
        this.todoName = '';
        this.error = '';
      } else {
        this.error = 'Необходимо название задачи'
      }
    },
    // Удаляет задачу из массива с помощью филтрации.
    deleteTodoItem(itemToDelete) {
      this.todoItems = this.todoItems.filter(todoItem => todoItem != itemToDelete);
    },
    // Переводит элемент списка в режим редактирования.
    editTodoItem(itemToEdit) {
      this.beforeEdit = itemToEdit.name;
      itemToEdit.edit = true;
    },
    // Сохраняет изменения при редактировании и возвращает к исходному виду.
    saveChanges(itemToEdit) {
        if(itemToEdit.name.trim() == '') {
          itemToEdit.name = this.beforeEdit;
        }
        itemToEdit.edit = false;
    },
    // Отменяет изменения совершённые при редактировании и возвращает к исходному виду.
    cancelChanges(itemToEdit) {
      itemToEdit.name = itemToEdit.beforeEdit;
      itemToEdit.edit = false;
    }
  }
}
</script>

<style src="./app.css"></style>
