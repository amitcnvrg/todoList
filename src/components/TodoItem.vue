<template>
    <div class="todo-item">
        <div class=todo-item-textbox>
                <input type="checkbox" v-model="todo.completed" >
                <div v-if="!todo.editing" class="todo-item-label" 
                :class="{'todo-item-completed': todo.completed}" @dblclick="editTodo(index)" > 
                    {{ todo.title }} 
                    </div>
                <input v-else-if="todo.editing" class="todo-item-edit" type="text" :value="title" @input="setTitle"
                @keyup.enter="doneEdit(index)" @blur="doneEdit(index)" @keyup.esc="cancelEdit(index)" v-focus>
            </div>

            <div class="remove-item" @click="removeTodo(index)">
                &times;
            </div>
    </div>
</template>


<script>
export default {
    name: 'todo-item',
    props: {
        todo: {
            type: Object,
            required: true,
        },
        index: {
            type: Number,
            required: true,
        },
        checkAll:{
            type: Boolean,
            required: true,
        },
        removeTodo: {
            type: Function,
            required: true,
        },
        editTodo:{
            type: Function,
            required: true,
        },
        doneEdit:{
            type: Function,
            required: true,
        },
        cancelEdit:{
            type: Function,
            required: true,
        },

    },
    data(){
        return {
            // 'id': this.todo.id, 
            // 'title': this.todo.title,
            // 'completed': this.todo.completed,
            // 'editing': this.todo.editing,
            // 'titleBeforeEdit': '',
            title: this.todo.title
        }
    },

    directives:{ //understand this part (related to "@blur=... ")
      focus:{
          inserted: function (el) {
              el.focus()
          }
      }
    },
    methods: {
        setTitle(event) {
            this.title = event.target.value;
        }
    }
}
</script>
