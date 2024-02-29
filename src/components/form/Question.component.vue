<template>
	<div class="question-content">
		<div>
			<label class="font-medium" :for="'formQuestion' + index">{{ 'Pregunta ' + (index + 1).toString().padStart(2,
				'0') }}</label>
			<input type="text" :name="'formQuestion' + index" :id="'formQuestion' + index" v-model="pregunta.texto" />
		</div>
		<div>
			<label class="font-medium" :for="'formQuestion' + index + 'Type'">Tipo de pregunta</label>
			<select :name="'formQuestion' + index + 'Type'" :id="'formQuestion' + index + 'Type'" v-model="pregunta.tipo">
				<option value="text">Texto</option>
				<option value="radio">Opción</option>
				<option value="range">Rango (Ejm. 0-5)</option>
				<option value="multiple">Casilla</option>
				<option value="subsection">Subtítulo</option>
			</select>
		</div>
		<Respuesta v-for="(respuesta, indexRespuesta) in pregunta.respuestas" :key="indexRespuesta" :respuesta="respuesta"
			:index="indexRespuesta" v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple'" />
		<div v-if="pregunta.tipo === 'range'">
			<label class="font-medium" :for="'formQuestion' + index + 'Answer'">Rango</label>
			<div class="slider-container">
				<input type="range" min="0" max="10" step="1" :name="'formQuestion' + index + 'Answer'"
					:id="'formQuestion' + index + 'Answer'" v-model.number="pregunta.rango" class="slider" />
				<div class="slider-value">{{ pregunta.rango }}</div>
			</div>
		</div>

		<div v-if="pregunta.tipo === 'text'">
			<p>Este tipo de pregunta no tiene respuestas</p>
		</div>

		<div v-if="pregunta.tipo === 'subsection'">
			<p>Subtítulo no tiene respuestas</p>
		</div>

		<div class="buttons-content">
			<button @click="$emit('addAnswer', index)" class=""
				v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple'">
				Agregar Respuesta
			</button>

			<button @click="$emit('deleteLastAnswer', index, indexPregunta)" class=""
				v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple'">
				Eliminar Respuesta
			</button>
		</div>

		<button @click="openModal" class="delete-button">
			<i class="fas fa-times"></i>
		</button>

		<transition name="modal">
			<div v-if="modal" class="modal">
				<div class="modal-content">
					<p>¿Estás seguro de que quieres eliminar esta pregunta?</p>
					<div class="modal-buttons">
						<button @click="$emit('deleteQuestion')">Si</button>
						<button @click="closeModal">No</button>
					</div>
				</div>
			</div>
		</transition>
	</div>
</template>

<script>
import Respuesta from './Answer.component.vue';

export default {
	name: 'Pregunta',
	data() {
		return {
			modal: false
		}
	},
	methods: {
		openModal() {
			this.modal = true;
		},
		closeModal() {
			this.modal = false;
		}
	},
	components: {
		Respuesta
	},
	props: ['pregunta', 'index'],
	watch: {
		'pregunta.tipo': function(newVal, oldVal) {
			if (newVal !== oldVal) {
				this.pregunta.respuestas = [];
			}
		}
	}
}
</script>
