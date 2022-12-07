<script setup>
import { reactive } from 'vue';
import { computed } from '@vue/reactivity';

import useVuelidate from "@vuelidate/core";
import { required, email, minLength, sameAs, numeric, between } from '@vuelidate/validators';

const userForm = reactive({
  username: '',
  email: '',
  password: '',
  age: 0,
  terms: false,
})

const userFormRules = computed(() => {
  return {
    username: { required, minLength: minLength(3) }, //!! zorunlu ve minimum 3 karakter girilmeli
    email: { required, email }, //!! zorunlu ve email girilmeli
    password: { required }, //!! zorunlu
    age: { required, numeric, between, between: between(1, 99) }, //!! yaş zorunlu, rakam olmalı ve 1-99 arası olmalı.
    terms: { sameAs: sameAs(true) }, //!! koşullar kabul edildiyse otomatik true olacak. koşullar kabul edilmiş mi?
  }
})

const user$ = useVuelidate(userFormRules, userForm)

const register = async () => {
  const result = await user$.value.$validate(); 

  if (!result) return;

  alert("kayıt başarılı");
}

</script>

<template>

  <!-- <pre>
    {{ user$.$errors }} 
  </pre> -->

  <div class="flex items-center justify-center min-h-screen bg-slate-200">
    <div class="Form border bg-white p-8 rounded-lg md:w-[600px]">
      <h3 class="text-3xl text-slate-900 font-bold my-4">kaydol</h3>

      <div class="mb-4">
        <div class="mb-1 text-slate-600">kullanıcı adı</div>

        <input type="text" class="Input" placeholder="kullanıcı adı" @input="user$.username.$touch()"
          v-model="userForm.username">

        <span v-for="error in user$.username.$errors" :key="error.$uid" class="text-red-600">
          {{ error.$message }} 
        </span>

      </div>


      <div class="mb-4">
        <div class="mb-1 text-slate-600">e-posta</div>
        <input type="text" class="Input" placeholder="e-posta" v-model="userForm.email">
        
        <p v-if="(user$.email.$error)" class="text-red-600">geçerli bir eposta
          girin.</p>

      </div>


      <div class="mb-4">
        <div class="mb-1 text-slate-600">yaş</div>
        <input type="text" class="Input" placeholder="yaş" v-model="userForm.age">
        <span v-for="error in user$.age.$errors" class="text-red-600 ">
          {{ error.$message }}
          <br>
        </span>
      </div>


      <div class="mb-4">
        <div class="mb-1 text-slate-600">parola</div>
        <input type="password"
          class="Input"
          placeholder="parola" :class="{ 'border-red-600': user$.password.$error }" v-model="userForm.password">
      </div>

      <div class="mb-4">
        <input type="checkbox" class="mr-2 cursor-pointer accent-emerald-400" placeholder="kullanıcı adı" id="terms"
          v-model="userForm.terms">
        <label for="terms" class="text-slate-900 underline cursor-pointer"
          :class="{ 'text-red-600': user$.terms.$error }">şartları
          onaylıyorum ve kabul
          ediyorum.</label>
      </div>


      <div class="mb-4">
        <button
          class="w-full bg-transparent rounded-lg font-bold bg-slate-900 hover:bg-slate-800 py-2 text-slate-200 transition-all duration-500"
          @click.prevent="register">
          kaydol
        </button>
      </div>

    </div>
  </div>

</template>

<style scoped>
.Input {
  @apply w-full bg-transparent rounded-lg border border-slate-200 py-2 px-2 focus:px-4 placeholder-slate-400 focus:ring-0 outline-none focus:border-slate-600 transition-all duration-500
}
</style>