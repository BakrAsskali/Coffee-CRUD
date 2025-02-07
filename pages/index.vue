<script setup lang="ts">
import { ref, onMounted } from "vue";

// Coffee Data Type
type Coffee = {
  id: string;
  name: string;
  description: string;
  price: number;
};

// State
const coffees = ref<Coffee[]>([]);
const newCoffee = ref({ name: "", description: "", price: 0 });
const editingCoffee = ref<Coffee | null>(null);

// Fetch Coffees
const fetchCoffees = async () => {
  const res = await fetch("http://localhost:3001/coffees");
  coffees.value = await res.json();
};

// Add Coffee
const addCoffee = async () => {
  await fetch("http://localhost:3001/coffees", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(newCoffee.value),
  });
  newCoffee.value = { name: "", description: "", price: 0 };
  fetchCoffees();
};

// Edit Coffee
const editCoffee = (coffee: Coffee) => {
  editingCoffee.value = { ...coffee };
};

// Update Coffee
const updateCoffee = async () => {
  if (!editingCoffee.value) return;
  await fetch(`http://localhost:3001/coffees/${editingCoffee.value.id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(editingCoffee.value),
  });
  editingCoffee.value = null;
  fetchCoffees();
};

// Delete Coffee
const deleteCoffee = async (id: string) => {
  await fetch(`http://localhost:3001/coffees/${id}`, { method: "DELETE" });
  fetchCoffees();
};

// Load Data on Page Load
onMounted(fetchCoffees);
</script>

<template>
  <div class="max-w-3xl mx-auto p-6 bg-white shadow-md rounded-lg">
    <h1 class="text-2xl font-bold mb-4">Coffee CRUD</h1>

    <!-- Coffee Form -->
    <div class="mb-4">
      <input v-model="newCoffee.name" type="text" placeholder="Name" class="border p-2 rounded w-full mb-2" />
      <input v-model="newCoffee.description" type="text" placeholder="Description" class="border p-2 rounded w-full mb-2" />
      <input v-model="newCoffee.price" type="number" placeholder="Price" class="border p-2 rounded w-full mb-2" />
      <button @click="addCoffee" class="bg-blue-500 text-white p-2 rounded w-full">Add Coffee</button>
    </div>

    <!-- Coffee List -->
    <table class="w-full border-collapse border border-gray-300">
      <thead>
      <tr class="bg-gray-100">
        <th class="border p-2">Name</th>
        <th class="border p-2">Description</th>
        <th class="border p-2">Price</th>
        <th class="border p-2">Actions</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="coffee in coffees" :key="coffee.id" class="border">
        <td class="p-2">{{ coffee.name }}</td>
        <td class="p-2">{{ coffee.description }}</td>
        <td class="p-2">${{ coffee.price }}</td>
        <td class="p-2">
          <button @click="editCoffee(coffee)" class="bg-yellow-500 text-white p-1 rounded mx-1">Edit</button>
          <button @click="deleteCoffee(coffee.id)" class="bg-red-500 text-white p-1 rounded">Delete</button>
        </td>
      </tr>
      </tbody>
    </table>

    <!-- Edit Coffee Form -->
    <div v-if="editingCoffee" class="mt-4">
      <h2 class="text-lg font-bold">Edit Coffee</h2>
      <input v-model="editingCoffee.name" type="text" class="border p-2 rounded w-full mb-2" />
      <input v-model="editingCoffee.description" type="text" class="border p-2 rounded w-full mb-2" />
      <input v-model="editingCoffee.price" type="number" class="border p-2 rounded w-full mb-2" />
      <button @click="updateCoffee" class="bg-green-500 text-white p-2 rounded w-full">Update Coffee</button>
    </div>
  </div>
</template>
