<template>
	<div v-for="(seccion, index) in secciones" :key="index" class="part-seccion">
		<div class="part-seccion-title">
			<label :for="'formSection' + index">Nombre de la Sección</label>
			<input type="text" :name="'formSection' + index" :id="'formSection' + index" v-model="seccion.nombre" />
		</div>
		<div class="part-seccion-content">
			<Pregunta v-for="(pregunta, indexPregunta) in seccion.preguntas" :key="indexPregunta" :pregunta="pregunta"
				:index="indexPregunta" @addQuestion="addQuestion(index)" />
		</div>
		<button @click="openModal" class="delete-button">
			<i class="fas fa-times"></i>
		</button>
	</div>

	<div class="button-content">
		<button @click="addSection" class="button">
			<i class="fas fa-plus"></i>
			Agregar Sección
		</button>

	</div>

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
		},
		deleteSection(index) {
			this.secciones.splice(index, 1);
			this.modal = false;
		},
		addQuestion(indexSeccion) {
			this.secciones[indexSeccion].preguntas[0].respuestas.push({
				texto: ''
			});
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
.button-content {
	display: flex;
	justify-content: center;
	padding-top: 5px;
	width: 100%;
	border-top: 1px solid #d3d2d2;
}

.button {
	background-color: #000;
	border: none;
	color: white;
	padding: 10px 20px;
	text-align: center;
	text-decoration: none;
	display: inline-block;
	font-size: 16px;
	margin: 4px 2px;
	transition-duration: 0.4s;
	cursor: pointer;
	border-radius: 5px;
}

.button:hover {
	background-color: #b31e23;
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

	border: 1px solid #d3d2d2;
	border-radius: 5px;
	padding: 1.2rem 1rem;
	margin-bottom: 1rem;
}

.part-seccion-title {
	width: 100%;
	display: flex;
	flex-direction: column;
	align-items: flex-start;
}

.part-seccion-content {
	width: 100%;
	display: flex;
	flex-flow: column wrap;
	align-items: flex-start;
}

.part-seccion-content div {
	width: 100%;
	display: flex;
	flex-flow: column nowrap;
}

.part-seccion-content .question-content {
	display: grid;
	grid-template-columns: 1fr 1fr;
	gap: 1rem;
}

.delete-button{
	position: absolute;
	top: 0px;
	right: 0px;
	color: #000;
	font-size: 1.2rem;
	width: 23px;
	height: 23px;
	background-color: #f9f9f9;

	display: flex;
	justify-content: center;
	align-items: center;
}

.delete-button:hover{
	color: #ff3d42;
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
}

.modal-content {
	background-color: #fff;
	padding: 1rem;
	border-radius: 5px;
	box-shadow: 0 0 5px 0 #d3d2d2;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	gap: 1rem;
}

.modal-buttons {
	display: flex;
	justify-content: center;
	align-items: center;
	gap: 1rem;
}

.modal-buttons button{
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