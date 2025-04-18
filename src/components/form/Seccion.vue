<template>
	<div v-for="(seccion, index) in secciones" :key="index" class="part-seccion">
		<div class="part-seccion-title">
			<div>
				<label class="text-xl font-medium" :for="'formSection' + index">Nombre de la Sección</label>
				<input type="text" :name="'formSection' + index" :id="'formSection' + index" v-model="seccion.nombre" />
			</div>
			<div class="buttons-content">
				<button @click="addQuestion(index)" class="">
					Agregar Pregunta
				</button>
				<button @click="$emit('duplicateSection', index)" class="">
					Duplicar Sección
				</button>
				<button class="up-button" @click="moveSectionUp(index)" v-if="index > 0">↑</button>
				<button class="down-button" @click="moveSectionDown(index)"
					v-if="index < secciones.length - 1">↓</button>
			</div>
		</div>
		<div class="part-seccion-content">
			<Pregunta v-for="(pregunta, indexPregunta) in seccion.preguntas" :key="indexPregunta" :pregunta="pregunta"
				:index="indexPregunta" :totalPreguntas="seccion.preguntas.length"
				@addAnswer="addAnswer(index, indexPregunta)" @deleteLastAnswer="deleteLastAnswer(index, indexPregunta)"
				@deleteQuestion="deleteQuestion(index, indexPregunta)"
				@duplicateQuestion="duplicateQuestion(index, indexPregunta)"
				@moveQuestionUp="moveQuestionUp(index, indexPregunta)"
				@moveQuestionDown="moveQuestionDown(index, indexPregunta, seccion.preguntas.length)" />
		</div>
		<button @click="openModal(index)" class="delete-button">
			<i class="fas fa-times"></i>
		</button>
	</div>

	<button @click="addSection" class="button">
		Agregar Sección
	</button>

	<transition name="modal">
		<div v-if="modal" class="modal">
			<div class="modal-content">
				<p>¿Estás seguro de que quieres eliminar esta sección?</p>
				<div class="modal-buttons">
					<button @click="deleteSection">Si</button>
					<button @click="closeModal">No</button>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>
import Pregunta from '../form/Question.component.vue';

export default {
	name: 'Seccion',
	components: {
		Pregunta
	},
	props: ['secciones'],
	data() {
		return {
			modal: false,
			indexToDelete: null
		}
	},
	methods: {
		addSection() {
			this.secciones.push({
				nombre: '',
				preguntas: [{
					texto: '',
					tipo: 'text',
					respuestas: [],
					rango: {
						min: 0,
						max: 0
					},
					download: '',
					optional: ''
				}]
			});
			this.$emit('update:secciones', this.secciones);
		},
		deleteSection() {
			this.secciones.splice(this.indexToDelete, 1);
			this.modal = false;
			this.$emit('update:secciones', this.secciones);
			this.indexToDelete = null;
			this.closeModal();
		},
		addAnswer(indexSeccion, indexPregunta) {
			this.secciones[indexSeccion].preguntas[indexPregunta].respuestas.push({
				texto: ''
			});
		},
		deleteLastAnswer(indexSeccion, indexPregunta) {
			this.secciones[indexSeccion].preguntas[indexPregunta].respuestas.pop();
			this.closeModal();
		},
		addQuestion(indexSeccion) {
			this.secciones[indexSeccion].preguntas.push({
				texto: '',
				tipo: 'text',
				respuestas: [],
				rango: {
					min: 0,
					max: 0
				}
			});
		},
		deleteQuestion(indexSeccion, indexPregunta) {
			this.secciones[indexSeccion].preguntas.splice(indexPregunta, 1);
		},
		duplicateQuestion(indexSeccion, indexPregunta) {
			const pregunta = this.secciones[indexSeccion].preguntas[indexPregunta];
			const duplicatedPregunta = JSON.parse(JSON.stringify(pregunta));
			duplicatedPregunta.respuestas = Array.isArray(pregunta.respuestas)
				? pregunta.respuestas.map(respuesta => ({ texto: respuesta.texto || '' }))
				: [];
			this.secciones[indexSeccion].preguntas.splice(indexPregunta + 1, 0, duplicatedPregunta);
			this.$emit('update:secciones', this.secciones);
		},
		moveSectionUp(index) {
			if (index > 0) {
				const temp = this.secciones[index];
				this.secciones.splice(index, 1);
				this.secciones.splice(index - 1, 0, temp);
			}
		},
		moveSectionDown(index) {
			if (index < this.secciones.length - 1) {
				const temp = this.secciones[index];
				this.secciones.splice(index, 1);
				this.secciones.splice(index + 1, 0, temp);
			}
		},
		moveQuestionUp(indexSeccion, indexPregunta) {
			if (indexPregunta > 0) {
				const preguntas = this.secciones[indexSeccion].preguntas;
				const temp = preguntas[indexPregunta];
				preguntas.splice(indexPregunta, 1);
				preguntas.splice(indexPregunta - 1, 0, temp);
			}
		},
		moveQuestionDown(indexSeccion, indexPregunta, totalPreguntas) {
			if (indexPregunta < totalPreguntas - 1) {
				const preguntas = this.secciones[indexSeccion].preguntas;
				const temp = preguntas[indexPregunta];
				preguntas.splice(indexPregunta, 1);
				preguntas.splice(indexPregunta + 1, 0, temp);
			}
		},
		openModal(index) {
			this.indexToDelete = index;
			this.modal = true;
		},
		closeModal() {
			this.modal = false;
		}
	}
}
</script>

<style>
.part-seccion {
	position: relative;
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	gap: 1rem;
	width: 100%;
	border: 1px solid #0265b2;
	border-radius: 5px;
	padding: 1.2rem 1rem;
	margin-bottom: 1rem;
}

.part-seccion-title {
	width: 100%;
	display: flex;
	flex-direction: row;
	align-items: flex-start;
	justify-content: space-between;
	gap: 1rem;
}

.part-seccion-title div {
	width: 100%;
	display: flex;
	flex-direction: column;
	align-items: flex-start;
}

.part-seccion-title button {
	width: 150px;
	background-color: #0265b2;
	color: #fff;
	cursor: pointer;
	border: none;
	border-radius: 5px;
	padding: 0.6rem 0;
	transition: all 0.3s ease;
}

.part-seccion-title button:hover {
	background-color: #0280e0;
}

.part-seccion-content {
	width: 100%;
	display: flex;
	flex-direction: column;
	align-items: flex-start;
	gap: 1rem;
}

.part-seccion-content .question-content {
	position: relative;
	width: 100%;
	display: grid;
	grid-template-columns: 3fr 1fr;
	gap: 1rem;
	border: 1px solid #d3d2d2;
	border-radius: 5px;
	padding: 1rem;
}

.part-seccion-content .question-content .answer-content {
	grid-column: 1 / 3;
	display: flex;
	flex-direction: row;
	justify-content: center;
	align-items: center;
	gap: 1rem;
	margin: 1rem 0;
}

.part-seccion-content .question-content .answer-content input {
	width: 100%;
}

.buttons-content {
	display: flex;
	flex-direction: row !important;
	align-items: center !important;
	justify-content: center !important;
	transform: translateY(10px);
	gap: 1rem;
}

.delete-button {
	position: absolute;
	top: 3px;
	right: 5px;
	color: #000;
	font-size: 1.2rem;
	width: 23px;
	height: 23px;
	background-color: #f9f9f9;
	display: flex;
	justify-content: center;
	align-items: center;
	transition: all 0.2s linear;
}

.delete-button:hover {
	color: #ff3d42;
	transform: scale(1.1);
}

.modal-enter-active,
.modal-leave-active {
	transition: opacity 0.2s;
}

.modal-enter-from,
.modal-leave-to {
	opacity: 0;
}

.modal {
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: rgba(0, 0, 0, 0.5);
	display: flex;
	justify-content: center;
	align-items: center;
	z-index: 1000;
}

.modal-content {
	max-width: 500px;
	background-color: #fff;
	padding: 1rem;
	border-radius: 5px;
	box-shadow: 0 12px 16px 0 rgba(0, 0, 0, 0.24), 0 17px 50px 0 rgba(0, 0, 0, 0.19);
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	text-align: center;
	gap: 1rem;
}

.modal-buttons {
	display: flex;
	flex-direction: row;
	justify-content: center;
	align-items: center;
	gap: 1rem;
}

.modal-buttons button {
	width: 120px;
	border-radius: 5px;
	padding: 0.5rem;
	border: 1px solid #d3d2d2;
	cursor: pointer;
}

.modal-buttons button:first-child {
	background-color: #fff;
	color: #000;
	transition: all 0.3s ease;
}

.modal-buttons button:first-child:hover {
	color: #e5282f;
	border: 1px solid #e5282f;
}

.modal-buttons button:nth-child(2) {
	border: 1px solid #e5282f;
	background-color: #e5282f;
	color: #fff;
	transition: all 0.3s ease;
}

.modal-buttons button:nth-child(2):hover {
	background-color: #ff3d42;
}

.slider-container {
	position: relative;
	width: 100%;
}

.slider {
	width: 100%;
	height: 15px;
	border-radius: 5px;
	background: #d3d3d3;
	outline: none;
	appearance: none;
}

.slider::-webkit-slider-thumb {
	-webkit-appearance: none;
	appearance: none;
	width: 20px;
	height: 20px;
	border-radius: 50%;
	background: #0265b2;
	cursor: pointer;
	transition: background 0.3s ease-in-out;
}

.slider::-webkit-slider-thumb:hover {
	background: #97cdf7;
}

.slider-value {
	position: absolute;
	width: inherit;
	text-align: right;
	top: -20px;
	font-size: 14px;
	color: #000;
	font-weight: 700;
}

.range-container {
	display: flex;
	flex-direction: column;
	justify-content: space-between;
	gap: 0.5rem;
}

.range-container div {
	display: flex;
	flex-direction: row;
	justify-content: flex-start;
	align-items: center;
	gap: 1rem;
}

.range-container input {
	max-width: 100px;
	border: 1px solid #d3d2d2;
	border-radius: 5px;
	padding: 0.5rem;
}

.range-container label {
	width: 100%;
}

input,
select,
textarea {
	width: 100%;
}

select {
	padding: 0.5rem;
	border: 1px solid #d3d2d2;
	border-radius: 5px;
}

input[type="text"],
textarea {
	padding: 0.5rem;
	border: 1px solid #d3d2d2;
	border-radius: 5px;
}

.button {
	position: fixed;
	width: 160px;
	bottom: calc(16.7rem - 4px);
	right: calc(1rem - 2px);
	background-color: #000;
	border: none;
	color: white;
	padding: 1rem;
	text-align: center;
	text-decoration: none;
	font-size: 16px;
	margin: 4px 2px;
	transition-duration: 0.4s;
	cursor: pointer;
	border-radius: 5px;
}

.button:hover {
	background-color: #e5282f;
	box-shadow: 0 12px 16px 0 rgba(0, 0, 0, 0.24), 0 17px 50px 0 rgba(0, 0, 0, 0.19);
	color: white;
}

.up-button {
	background-color: #208560 !important;
	width: 50px !important;
	color: #ffffff !important;
	font-weight: bold !important;
	padding: 0.5rem 1rem !important;
	border-radius: 5px !important;
}

.up-button:hover {
	background-color: #25a174 !important;
}

.down-button {
	background-color: #e5282f !important;
	width: 50px !important;
	border-radius: 5px !important;
	color: #ffffff !important;
	font-weight: bold !important;
	padding: 0.5rem 1rem !important;
}

.down-button:hover {
	background-color: #ff3d42 !important;
}
</style>