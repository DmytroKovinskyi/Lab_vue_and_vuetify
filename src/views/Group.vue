<script setup lang="ts">
import { ref } from 'vue'

interface Student {
  id: number
  name: string
  birthYear: number
  phone: string
  email: string
}

const students = ref<Student[]>([
  { id: 1, name: 'John Doe', birthYear: 2000, phone: '123-456-7890', email: 'john@example.com' },
  { id: 2, name: 'Jane Smith', birthYear: 1999, phone: '987-654-3210', email: 'jane@example.com' },
])

const newStudent = ref<Student>({
  id: 0,
  name: '',
  birthYear: new Date().getFullYear(),
  phone: '',
  email: '',
})

const searchQuery = ref('')
const editedStudent = ref<Student | null>(null)
const isEditDialogOpen = ref(false)

function addStudent() {
  if (newStudent.value.name.trim() && newStudent.value.email.trim()) {
    students.value.push({
      ...newStudent.value,
      id: Date.now(),
    })
    resetForm()
  }
}

function openEditDialog(student: Student) {
  editedStudent.value = { ...student }
  isEditDialogOpen.value = true
}

function saveEditedStudent() {
  if (editedStudent.value) {
    const index = students.value.findIndex((s) => s.id === editedStudent.value!.id)
    if (index !== -1) {
      students.value[index] = { ...editedStudent.value }
    }
    closeEditDialog()
  }
}

function closeEditDialog() {
  editedStudent.value = null
  isEditDialogOpen.value = false
}

function deleteStudent(id: number) {
  students.value = students.value.filter((student) => student.id !== id)
}

function resetForm() {
  newStudent.value = {
    id: 0,
    name: '',
    birthYear: new Date().getFullYear(),
    phone: '',
    email: '',
  }
}
</script>

<template>
  <v-container>
    <h1>My Group</h1>

    <v-form>
      <v-row>
        <v-col cols="12" md="3">
          <v-text-field v-model="newStudent.name" label="Full Name" clearable></v-text-field>
        </v-col>
        <v-col cols="12" md="2">
          <v-text-field
              v-model="newStudent.birthYear"
              label="Birth Year"
              type="number"
              clearable
          ></v-text-field>
        </v-col>
        <v-col cols="12" md="3">
          <v-text-field v-model="newStudent.phone" label="Phone" clearable></v-text-field>
        </v-col>
        <v-col cols="12" md="4">
          <v-text-field v-model="newStudent.email" label="Email" clearable></v-text-field>
        </v-col>
      </v-row>
      <v-btn color="primary" @click="addStudent">Add Student</v-btn>
    </v-form>

    <v-text-field
        v-model="searchQuery"
        label="Search"
        prepend-inner-icon="mdi-magnify"
        variant="outlined"
        hide-details
        single-line
        clearable
        class="mt-4"
    ></v-text-field>

    <v-data-table
        :items="students"
        :headers="[
         { title: 'Full Name', value: 'name', sortable: true },
         { title: 'Birth Year', value: 'birthYear', sortable: true },
         { title: 'Phone', value: 'phone', sortable: true },
         { title: 'Email', value: 'email', sortable: true },
         { title: 'Actions', value: 'actions', sortable: false },
       ]"
        :search="searchQuery"
        class="mt-4"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-btn icon color="blue" @click="openEditDialog(item)">
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon color="red" @click="deleteStudent(item.id)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <v-dialog v-model="isEditDialogOpen" max-width="600px">
      <v-card v-if="editedStudent">
        <v-card-title>Edit Student</v-card-title>
        <v-card-text>
          <v-form>
            <v-text-field v-model="editedStudent.name" label="Full Name" clearable></v-text-field>
            <v-text-field
                v-model="editedStudent.birthYear"
                label="Birth Year"
                type="number"
                clearable
            ></v-text-field>
            <v-text-field v-model="editedStudent.phone" label="Phone" clearable></v-text-field>
            <v-text-field v-model="editedStudent.email" label="Email" clearable></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="blue" text @click="saveEditedStudent">Save</v-btn>
          <v-btn color="red" text @click="closeEditDialog">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>