<script setup lang="ts">
import { ref } from 'vue'

const shoppingList = ref([
  { id: 1, title: 'Milk', bought: false },
  { id: 2, title: 'Bread', bought: false },
  { id: 3, title: 'Eggs', bought: false }
])

const settingsSelection = ref(null)
const newItemTitle = ref('')

function toggleBought(item: { id: number; title: string; bought: boolean }) {
  item.bought = !item.bought
}

function deleteItem(id: number) {
  shoppingList.value = shoppingList.value.filter((item) => item.id !== id)
}

function addNewItem() {
  if (newItemTitle.value.trim() !== '') {
    shoppingList.value.push({
      id: Date.now(),
      title: newItemTitle.value.trim(),
      bought: false,
    })
    newItemTitle.value = '' // Clear the input field
  }
}
</script>

<template>
  <header class="list_header">
    <h1>Shopping List</h1>
  </header>
  <main>
    <v-text-field
        v-model="newItemTitle"
        label="Add new item"
        append-icon="mdi-plus"
        clearable
        @click:append="addNewItem"
    ></v-text-field>

    <v-list v-model:selected="settingsSelection" lines="three" select-strategy="leaf">
      <div v-for="(item, index) in shoppingList" :key="item.id">
        <v-list-item
            :class="{ 'blue lighten-5': item.bought }"
            :value="item.id"
            @click="toggleBought(item)"
        >
          <template v-slot:prepend>
            <v-list-item-action start>
              <v-checkbox-btn
                  v-model="item.bought"
                  @click.stop="toggleBought(item)"
              ></v-checkbox-btn>
            </v-list-item-action>
          </template>
          <v-list-item-title
              :class="{ 'text-decoration-line-through': item.bought }"
          >
            {{ item.title }}
          </v-list-item-title>
          <template v-slot:append>
            <v-list-item-action>
              <v-btn icon @click.stop="deleteItem(item.id)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </v-list-item-action>
          </template>
        </v-list-item>
        <v-divider v-if="index < shoppingList.length - 1" />
      </div>
    </v-list>
  </main>
</template>

<style>
@media (min-width: 1024px) {
  .list_header {
    min-height: auto;
    display: block;
    text-align: center;
  }
}
</style>