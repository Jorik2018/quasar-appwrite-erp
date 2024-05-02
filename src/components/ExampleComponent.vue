<template>
  <div>
    <p>{{ title }} </p>
    <ul>
      <li v-for="todo in todos" :key="todo.id" @click="increment">
        {{ todo.id }} - {{ todo.content }}
      </li>
    </ul>
    <p>Count: {{ todoCount }} / {{ meta.totalCount }}</p>
    <p>Active: {{ active ? 'yes' : 'no' }}</p>
    <p>Clicks on todos: {{ clickCount }}</p>
    <q-btn @click="save" color="primary" icon="mail" label="Save" />
    <QBtn @click="logout">Logout</QBtn>

  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import { Todo, Meta } from './models';
import { Account, Client, Databases, ID, Models } from 'appwrite';
console.log(process.env);
const client = new Client();

client
  .setEndpoint(process.env.APPWRITE_ENDPOINT || '')
  .setProject(process.env.APPWRITE_PROJECT || '');

const account = new Account(client);
const save = () => {
  const databases = new Databases(client);
  return databases.createDocument(
    process.env.APPWRITE_DATABASE || '',
    process.env.APPWRITE_BOOK_COLLECTION || '',
    ID.unique(),
    { 'title': 'Hamlet' }
  );
}

const loggedInUser = ref<Models.Preferences | null>(null);
const email = ref('');
const password = ref('');
const name = ref('');

const login = async (email: string, password: string) => {
  await account.createEmailPasswordSession(email, password);
  loggedInUser.value = await account.get();
};

const register = async () => {
  await account.create(ID.unique(), email.value, password.value, name.value);
  login(email.value, password.value);
};

const logout = async () => {
  await account.deleteSession('current');
  loggedInUser.value = null;
};

interface Props {
  title: string;
  todos?: Todo[];
  meta: Meta;
  active: boolean;
};

const props = withDefaults(defineProps<Props>(), {
  todos: () => []
});

const clickCount = ref(0);
function increment() {
  email.value = 'antoniopinedo2018@gmail.com';
  password.value = '12345678';
  name.value = '6632e417000101ab0743';
  console.log(register);
  login(email.value, password.value);
  clickCount.value += 1;
  return clickCount.value;
}

const todoCount = computed(() => props.todos.length);
</script>
