<template>
  <div>
    <p>{{ title }}</p>
    <!-- <ul>
      <li v-for="todo in todos" :key="todo.id" @click="increment">
        {{ todo.id }} - {{ todo.content }}
      </li>
    </ul>
    <p>Count: {{ todoCount }} / {{ meta.totalCount }}</p>
    <p>Active: {{ active ? 'yes' : 'no' }}</p>
    <p>Clicks on todos: {{ clickCount }}</p> -->
    <q-input filled v-model="emailField" label="Email" />
    <q-input filled v-model="passwordField" label="Password" />
    <q-btn :loading="loadingUserUid" color="white" text-color="black" label="Login"
      @click="loginWithEmailCall(emailField, passwordField)" />
    <p v-if="userUid != ''">Login response: {{ userUid }}</p>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  computed,
  ref,
  toRef,
  type PropType,
  type Ref,
} from 'vue';
import type { Todo, Meta } from './models';
import { loginWithEmail } from 'firebase-ts'

function useClickCount() {
  const clickCount = ref(0);
  function increment() {
    clickCount.value += 1
    return clickCount.value;
  }

  return { clickCount, increment };
}

function loginForm() {
  const emailField = ref('');
  const passwordField = ref('');

  return { emailField, passwordField }
}

function login() {
  const userUid = ref('');
  const loadingUserUid = ref(false);
  function loginWithEmailCall(email: string, password: string) {
    loadingUserUid.value = true;
    loginWithEmail(email, password).then((uid) => {
      userUid.value = uid; loadingUserUid.value = false;
    }).catch((e) => { console.log(e) });
  }

  return { loginWithEmailCall, userUid, loadingUserUid }
}

function useDisplayTodo(todos: Ref<Todo[]>) {
  const todoCount = computed(() => todos.value.length);
  return { todoCount };
}

export default defineComponent({
  name: 'ExampleComponent',

  props: {
    title: {
      type: String,
      required: true
    },
    todos: {
      type: Array as PropType<Todo[]>,
      default: () => []
    },
    meta: {
      type: Object as PropType<Meta>,
      required: true
    },
    active: {
      type: Boolean
    }
  },

  setup(props) {
    return { ...useClickCount(), ...login(), ...loginForm(), ...useDisplayTodo(toRef(props, 'todos')) };
  }
});
</script>
