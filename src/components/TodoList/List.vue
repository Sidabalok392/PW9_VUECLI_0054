<template>
  <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
        <v-spacer></v-spacer>
        <v-btn color="success" dark @click="addForm()"> Tambah </v-btn>
      </v-card-title>
      <v-data-table :headers="headers" :items="todos" :search="search">
        <template v-slot:[`item.note`]="{ item }">
          <v-expansion-panels flat>
            <v-expansion-panel flat>
              <v-expansion-panel-header flat>
                <v-icon> mdi-note-multiple </v-icon>
              </v-expansion-panel-header>
              <v-expansion-panel-content :color="colorBar(item)">
                <v-layout>{{ item.note }}</v-layout>
              </v-expansion-panel-content>
            </v-expansion-panel>
          </v-expansion-panels>
        </template>

        <template v-slot:[`item.priority`]="{ item }">
          <v-btn icon @click="checkPriority(item, index)">
            <v-icon> mdi-information </v-icon>
          </v-btn>
          <v-snackbar
            :top="snackbar.top"
            :right="snackbar.right"
            :left="snackbar.left"
            :bottom="snackbar.bottom"
            :color="snackbar.color"
            :timeout="1000"
            v-model="snackbar.show"
            >{{ snackbar.message }}
          </v-snackbar>
        </template>
        <template v-slot:[`item.actions`]="{ item, index }">
          <v-btn color="green" small icon @click="editForm(item, index)">
            <v-icon>mdi-pencil</v-icon></v-btn
          >
          <v-btn color="red" small icon @click="deleteItem(item)">
            <v-icon>mdi-delete</v-icon></v-btn
          >
        </template>
      </v-data-table>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Form Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field v-model="formTodo.task" label="Task" required>
            </v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak Penting']"
              label="Priority"
              required
            >
            </v-select>
            <v-textarea v-model="formTodo.note" label="Note" required>
            </v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="resetForm()">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="save()">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="editDialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Edit Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field v-model="formTodo.task" label="Task" required>
            </v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak Penting']"
              label="Priority"
              required
            >
            </v-select>
            <v-textarea v-model="formTodo.note" label="Note" required>
            </v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="resetForm()">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="saveEdit()">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-main>
</template>

<script>
export default {
  name: "List",
  data() {
    return {
      search: null,
      dialog: false,
      editDialog: false,
      snackbar: {
        top: false,
        right: false,
        left: false,
        bottom: false,
        show: false,
        color: null,
        message: null,
      },
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Note", value: "note" },
        { text: "Actions", value: "actions" },
      ],
      todos: [
        {
          task: "Coding",
          priority: "Penting",
          note: "Code for your life",
        },
        {
          task: "Gaming",
          priority: "Tidak Penting",
          note: "Wasting Time",
        },
        {
          task: "Masak",
          priority: "Biasa",
          note: "Indomie saat coding terbaik gan",
        },
      ],
      formTodo: { task: null, priority: null, note: null },
    };
  },

  methods: {
    save() {
      this.todos.push(this.formTodo);
      this.resetForm();
      this.dialog = false;
    },

    saveEdit() {
      Object.assign(this.todos[this.edit], this.formTodo);
      this.resetForm();
      this.editDialog = false;
    },

    cancel() {
      this.resetForm();
      this.dialog = false;
      this.editDialog = false;
    },

    resetForm() {
      this.dialog = false;
      this.editDialog = false;
    },

    addForm(item = { task: null, priority: null, note: null }, index) {
      this.edit = index;
      this.formTodo = item;
      this.dialog = true;
    },

    editForm(item = { task: null, priority: null, note: null }, index) {
      this.edit = index;
      this.formTodo = item;
      this.editDialog = true;
    },

    deleteItem(index) {
      this.delete = index;
      this.todos.splice(this.delete, 1);
    },

    colorBar(item) {
      if (item.priority == "Penting") {
        return "red accent-4";
      } else if (item.priority == "Biasa") {
        return "green darken-4";
      } else if (item.priority == "Tidak Penting") {
        return "light-blue darken-4";
      }
    },

    checkPriority(item) {
      if (item.priority == "Penting") {
        this.snackbar = {
          top: true,
          right: true,
          left: false,
          bottom: false,
          show: true,
          color: "red accent-4",
          message: "Penting",
        };
      } else if (item.priority == "Biasa") {
        this.snackbar = {
          top: true,
          right: false,
          left: true,
          bottom: false,
          show: true,
          color: "green darken-4",
          message: "Biasa",
        };
      } else if (item.priority == "Tidak Penting") {
        this.snackbar = {
          top: false,
          right: false,
          left: false,
          bottom: true,
          show: true,
          color: "light-blue darken-4",
          message: "Tidak Penting",
        };
      }
    },
  },
};
</script>
