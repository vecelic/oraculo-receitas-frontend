<template>
 <div class="generator-container">
  <h2>Oráculo de Receitas</h2>
  <p class="subtitle">Digite seus ingredientes (separados por vírgula):</p>

  <form @submit.prevent="generateRecipe" class="input-form">
    <textarea v-model="ingredients" class="ingredient-input" placeholder="ex: frango, arroz, tomate"></textarea>
    <button type="submit" class="submit-button">Gerar Receita!</button>
  </form>

  <p v-if="isLoading" class="status-message">Invocando o Oráculo, por favor aguarde...</p>
  <p v-if="errorMessage" class="status-message error">{{ errorMessage }}</p>

  <div v-if="recipe" class="result-display">
    <h2>Receita Gerada:</h2>
    <div class="recipe-text" v-html="recipe"></div>
  </div>
    </div>
  </template>

<script setup>
 import { marked } from 'marked'
 import axios from 'axios'
 import { ref } from 'vue'

 const isLoading = ref(false)
 const errorMessage = ref('')
 const ingredients = ref('')
 const recipe = ref('')

 const generateRecipe = async () => {
    isLoading.value = true;
    errorMessage.value = '';
    recipe.value = ''; 
    try {
  const response = await axios.post(
   'http://127.0.0.1:8000/api/generate/',
    {
        ingredients: ingredients.value
          }
  );
  recipe.value = response.data.title;
  const rawMarkdown = response.data.recipe_text;
    recipe.value = marked(rawMarkdown);
} 
catch (error) {
    errorMessage.value = 'O Oráculo está silencioso. Houve um erro ao gerar a receita.';
} 
finally{
    isLoading.value = false;
}
 }
</script>

<style scoped>
 .generator-container {
  max-width: 700px;
  margin: 40px auto; /* Centraliza o contêiner na página */
  padding: 30px;
  background-color: var(--cor-grimorio);
  border-radius: 10px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5); /* Sombra para dar profundidade */
  text-align: center;
}

h2 {
  font-family: var(--fonte-titulo);
  margin-bottom: 10px;
  color: var(--cor-ametista);
}

.subtitle {
  margin-bottom: 20px;
  opacity: 0.8;
}

.ingredient-input {
  width: 100%;
  min-height: 100px;
  padding: 15px;
  background-color: var(--cor-pedra-noturna);
  color: var(--cor-giz-arcano);
  border: 1px solid rgba(159, 122, 234, 0.3);
  border-radius: 5px;
  font-family: var(--fonte-corpo);
  font-size: 1rem;
  resize: vertical; /* Permite que o usuário redimensione a altura */
  margin-bottom: 20px;
}

.submit-button {
  background-color: var(--cor-ametista);
  color: white;
  border: none;
  padding: 15px 30px;
  border-radius: 50px; /* Botão em formato de pílula */
  font-size: 1.1rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease; /* Transição suave para o hover */
}

.submit-button:hover {
  transform: translateY(-2px); /* Efeito de flutuar */
  box-shadow: 0 5px 15px rgba(159, 122, 234, 0.4);
}

.status-message {
  margin-top: 20px;
  font-style: italic;
}

.status-message.error {
  color: #e57373; /* Um vermelho para mensagens de erro */
}

.result-display {
  margin-top: 40px;
  padding: 20px;
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 5px;
  text-align: left;
  white-space: pre-wrap; /* Preserva as quebras de linha da receita */
}

.recipe-text {
  opacity: 0.9;
}
</style>