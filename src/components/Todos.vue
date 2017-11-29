<template>
  <div>
    <div class="row">
      <div class="col-md-12">
        <div class="well">
          <h2>Add a New Todo</h2>
          <form>
            <div class="form-group">
              <label for="todoitem">Task</label>
              <input type="text" v-model="input" @keyup.enter="add" @keyup.esc="clear" class="form-control" id="todoitem" placeholder="Todo Item" />
            </div>
            <button type="button" @click="add" class="btn btn-default">Add</button>
          </form>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-12">
        <h2>List</h2>
        <ul class="list-group">
          <li v-for="(todo, index) in todos" :key="index" class="list-group-item">
            <input type="checkbox" v-model="todo.complete" class="pull-left">
            <span v-bind:class="{ complete: todo.complete }">{{ todo.description }}</span>
            <i @click="remove(todo.id)" class="glyphicon glyphicon-remove pull-right"></i>
          </li>
        </ul>
      </div>
    </div>

    <!-- <footer>Total todos: {{todos.length}} || {{todos.filter(x => x.complete).length}} completed</footer> -->
  </div>
</template>

<script>
  const api = 'http://localhost:4000/api'

  export default {
    name: 'Todo',
    data () {
      return {
        todos: [],
        input: ''
      }
    },
    mounted () {
      this.$http.get(`${api}/todos`).then(res => {
        this.todos = res.body.data
      }, err => {
        this.todos = [{ id: 0, description: 'Connect to API', complete: false }]
        console.error(err)
      })
    },
    methods: {
      add () {
        const inputValue = this.input
        const todo = { description: inputValue, complete: false }
        this.todos.push(todo)

        this.$http.post(`${api}/todos`, { todo }, { headers: { 'content-type': 'application/json' } }).then(res => {
          this.todos = this.todos.map(x => {
            if (!x.id) {
              x.id = res.body.data.id
            }
            return x
          })
        }, err => {
          this.input = inputValue
          console.error(err)
        })

        this.clear()
      },
      clear () {
        this.input = ''
      },
      remove (id) {
        const oldTodos = this.todos
        this.todos = this.todos.filter(x => x.id !== id)

        this.$http.delete(`${api}/todos/${id}`).then(res => {
        }, err => {
          this.todos = oldTodos
          console.error(err)
        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    margin: 5px 20px;
    /*background-color: #d9534f;*/
  }
  a {
    color: #42b983;
  }
  .complete {
    color: #bbb;
    text-decoration: line-through;

  }
</style>
