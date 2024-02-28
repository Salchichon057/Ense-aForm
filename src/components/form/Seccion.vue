<template>
	<div v-for="(seccion, index) in secciones" :key="index" class="part-seccion">
		<div class="part-seccion-title">
			<div>
				<label class="text-xl font-medium" :for="'formSection' + index">Nombre de la Sección</label>
				<input type="text" :name="'formSection' + index" :id="'formSection' + index" v-model="seccion.nombre" />
			</div>
			<button @click="addQuestion(index)" class="">
				Agregar Pregunta
			</button>
		</div>
		<div class="part-seccion-content">
			<Pregunta v-for="(pregunta, indexPregunta) in seccion.preguntas" :key="indexPregunta" :pregunta="pregunta"
				:index="indexPregunta" @addAnswer="addAnswer(index, indexPregunta)"
				@deleteLastAnswer="deleteLastAnswer(index, indexPregunta)"
				@deleteQuestion="deleteQuestion(index, indexPregunta)" />


		</div>
		<button @click="openModal" class="delete-button">
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
					<button @click="deleteSection(index)">Si</button>
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
	data() {
		return {
			secciones: [],
			modal: false
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
					rango: null
				}]
			});
			this.$emit('update:secciones', this.secciones);
		},
		deleteSection(index) {
			this.secciones.splice(index, 1);
			this.modal = false;
			this.$emit('update:secciones', this.secciones);
		},
		addAnswer(indexSeccion, indexPregunta) {
			this.secciones[indexSeccion].preguntas[indexPregunta].respuestas.push({
				texto: ''
			});
		},
		deleteLastAnswer(indexSeccion, indexPregunta) {
			this.secciones[indexSeccion].preguntas[indexPregunta].respuestas.pop();
		},
		addQuestion(indexSeccion) {
			this.secciones[indexSeccion].preguntas.push({
				texto: '',
				tipo: 'text',
				respuestas: [],
				rango: null
			});
		},
		deleteQuestion(indexSeccion, indexPregunta) {
			this.secciones[indexSeccion].preguntas.splice(indexPregunta, 1);
		},
		openModal() {
			this.modal = true;
		},
		closeModal() {
			this.modal = false;
		}
	}
}
</script>

<style>
.buttons-content {
	grid-column: 1 / 3;
	display: flex;
	flex-flow: row nowrap !important;
	justify-content: center;
	gap: 1rem;
}

.buttons-content button {
	width: 200px;
	background-color: #0265b2;
	color: #fff;
	cursor: pointer;
	border: none;
	border-radius: 5px;
	padding: 0.5rem 0;
	transition: all 0.3s ease;
}

.buttons-content button:hover {
	background-color: #0280e0;
}

.button {
	position: fixed;
	width: 160px;
	bottom: 5rem;
	right: 1rem;
	background-color: #000;
	border: none;
	color: white;
	padding: 1rem;
	text-align: center;
	text-decoration: none;
	font-size: 16px;
	text-wrap: balance;
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

.part-seccion {
	position: relative;
	display: flex;
	flex-flow: column wrap;
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
	flex-flow: row nowrap;
	align-items: flex-start;
	justify-content: space-between;
	gap: 1rem;
	align-items: flex-end;
}

.part-seccion-title div {
	width: 100%;
	display: flex;
	flex-flow: column nowrap;
}

.part-seccion-title button {
	width: 170px;
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
	flex-flow: column wrap;
	align-items: flex-start;
	gap: 1rem;
}

.part-seccion-content div {
	width: 100%;
	display: flex;
	flex-flow: column nowrap;
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
	flex-flow: row nowrap;
	justify-content: center;
	align-items: center;
	gap: 1rem;
	margin: 1rem 0;

}

.part-seccion-content .question-content .answer-content input {
	width: 100%;
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
	scale: 1.1;
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
	flex-flow: row nowrap !important;
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
	transition: background .3s ease-in-out;
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
</style>