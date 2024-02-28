<template>
	<div class="answer-content font-medium">
		<label :for="'formQuestion' + index + 'Answer' + indexRespuesta">Respuesta</label>
		<input type="text" :name="'formQuestion' + index + 'Answer' + indexRespuesta"
			:id="'formQuestion' + index + 'Answer' + indexRespuesta" v-model="respuesta.texto" />
	</div>
</template>

<script>
export default {
	props: ['respuesta', 'index', 'indexRespuesta']
}
</script>