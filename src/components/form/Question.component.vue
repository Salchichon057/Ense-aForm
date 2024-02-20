<template>
	<div class="question-content">
		<div>
			<label :for="'formQuestion' + index">Pregunta {{ index + 1 }}</label>
			<input type="text" :name="'formQuestion' + index" :id="'formQuestion' + index" v-model="pregunta.texto" />
		</div>
		<div>
			<label :for="'formQuestion' + index + 'Type'">Tipo de pregunta</label>
			<select :name="'formQuestion' + index + 'Type'" :id="'formQuestion' + index + 'Type'" v-model="pregunta.tipo">
				<option value="text">Texto</option>
				<option value="radio">Opción</option>
				<option value="range">Opción de rango (Ejm. 1-5)</option>
				<option value="multiple">Casilla</option>
			</select>
		</div>
		<button @click="$emit('agregarRespuesta')" class="button"
			v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple'">
			<i class="fas fa-plus"></i>
			Agregar Respuesta
		</button>
		<Respuesta v-for="(respuesta, indexRespuesta) in pregunta.respuestas" :key="indexRespuesta" :respuesta="respuesta"
			:index="indexRespuesta" v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple'" />
		<div v-if="pregunta.tipo === 'range'">
			<label :for="'formQuestion' + index + 'Answer'">Rango</label>
			<input type="number" :name="'formQuestion' + index + 'Answer'" :id="'formQuestion' + index + 'Answer'"
				v-model="pregunta.rango" />
		</div>
	</div>
</template>

<script>
import Respuesta from './Answer.component.vue';

export default {
	components: {
		Respuesta
	},
	props: ['pregunta', 'index']
}
</script>