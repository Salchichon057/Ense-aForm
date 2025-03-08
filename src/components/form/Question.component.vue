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
				<option value="text-optional">Texto Opcional</option>
				<option value="radio">Opción</option>
				<option value="range">Rango (Ejm. 0-5)</option>
				<option value="multiple">Casilla</option>
				<option value="download">Link al Drive</option>
				<option value="upload">Subir Archivo</option>
				<option value="subsection">Subtítulo</option>
			</select>
		</div>
		<Respuesta v-for="(respuesta, indexRespuesta) in pregunta.respuestas" :key="indexRespuesta" :respuesta="respuesta"
			:index="indexRespuesta"
			v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple' || pregunta.tipo === 'text-optional'" />

		<div v-if="pregunta.tipo === 'range'">
			<label class="font-medium" :for="'formQuestion' + index + 'Answer'">Rango</label>
			<div class="range-container">
				<label :for="'formQuestion' + index + 'Answer'">min - max</label>
				<div>
					<input type="number" placeholder="min" v-model="pregunta.rango.min" min="0" max="10">
					<input type="number" placeholder="max" v-model="pregunta.rango.max" min="0" max="10">
				</div>
			</div>
		</div>

		<div v-if="pregunta.tipo === 'text'">
			<p>Este tipo de pregunta no tiene respuestas</p>
		</div>

		<div v-if="pregunta.tipo === 'text-optional'">
			<p>Debe de haber al menos una respuesta llamada "Otro" o "No"</p>
			<!-- agregar un campo para que aparezca la pregunta opcional -->
			<label class="font-medium" :for="'formQuestion' + index + 'Answer'">Respuesta Opcional</label>
			<input type="text" v-model="pregunta.optional" />
		</div>

		<div v-if="pregunta.tipo === 'subsection'">
			<p>Subtítulo no tiene respuestas</p>
		</div>

		<div v-if="pregunta.tipo === 'upload'">
			<p>Esta pregunta no tiene respuestas, el usuario podrá subir un archivo desde su dispositivo</p>
		</div>

		<div v-if="pregunta.tipo === 'download'">
			<label class="font-medium" :for="'formQuestion' + index + 'Answer'">Link al drive</label>
			<input type="text" v-model="pregunta.download" />
		</div>

		<div class="buttons-content">
			<button @click="$emit('addAnswer', index)" class=""
				v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple' || pregunta.tipo === 'text-optional'">
				Agregar Respuesta
			</button>

			<button @click="$emit('deleteLastAnswer', index, indexPregunta)" class=""
				v-if="pregunta.tipo === 'radio' || pregunta.tipo === 'multiple' || pregunta.tipo === 'text-optional'">
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
						<button @click="deleteQuestion">Si</button>
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
		},
		deleteQuestion() {
			this.$emit('deleteQuestion');
			this.closeModal();
		}
	},
	components: {
		Respuesta
	},
	props: ['pregunta', 'index'],
	watch: {
		'pregunta.tipo': function (newVal, oldVal) {
			if (newVal !== oldVal) {
				this.pregunta.respuestas = [];
				if (newVal !== 'range') {
					this.pregunta.rango = null;
				} else if (!this.pregunta.rango) {
					this.pregunta.rango = { min: 0, max: 0 };
				}
			}
		},
		'pregunta.rango.min': function (newVal, oldVal) {
			if (this.pregunta.rango && newVal > this.pregunta.rango.max) {
				alert('El valor mínimo no puede ser mayor que el valor máximo');
				this.pregunta.rango.min = oldVal;
			} else if (this.pregunta.rango && newVal === this.pregunta.rango.max && newVal !== 0) {
				alert('El valor mínimo no puede ser igual al valor máximo');
				this.pregunta.rango.min = oldVal;
			}
		},
		'pregunta.rango.max': function (newVal, oldVal) {
			if (this.pregunta.rango && newVal < this.pregunta.rango.min) {
				alert('El valor máximo no puede ser menor que el valor mínimo');
				this.pregunta.rango.max = oldVal;
			} else if (this.pregunta.rango && newVal === this.pregunta.rango.min && newVal !== 0) {
				alert('El valor máximo no puede ser igual al valor mínimo');
				this.pregunta.rango.max = oldVal;
			}
		}
	}
}
</script>
