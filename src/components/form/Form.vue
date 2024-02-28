<template>
	<section class="form-container" id="form-container">
		<div class="flex w-full flex-col justify-start">
			<div class="part">
				<label for="formType" class="text-xl font-medium">Selecciona el tipo de formulario</label>
				<select name="formType" id="formType">
					<option value="6">Formulario General</option>
					<option value="1">Formulario de Formación</option>
					<option value="2">Formulario de Cultura</option>
					<option value="3">Formulario de Acompañamiento</option>
					<option value="4">Formulario de Núcleo</option>
				</select>
			</div>

			<div class="date flex flex-col justify-start">
				<label for="formDate" class="text-xl font-medium">Fecha Límite</label>
				<input type="date" name="formDate" id="formDate" class="w-36" />
			</div>
		</div>
		<div class="part">
			<label for="formTitle" class="text-xl font-medium">Título del formulario</label>
			<input type="text" name="formTitle" id="formTitle" />
		</div>

		<div class="part">
			<label for="formDescription" class="text-xl font-medium">Descripción del formulario</label>
			<div class="textarea-container">
				<textarea name="formDescription" id="formDescription" rows="1" v-model="formDescription"
					@input="updateCharCount"></textarea>
				<div class="char-counter text-gray-500">{{ charCount }} / {{ maxCharCount }}</div>
			</div>
		</div>
		<Seccion @update:secciones="secciones = $event" />
	</section>
	<button class="create" @click="openModal">Crear Formulario</button>

	<!-- Crear un modal de confirmación -->
	<transition name="modal">
		<div v-if="modal" class="modal">
			<div class="modal-content">
				<p>Revisa todos los campos antes de crear el formulario. ¿Estás seguro de que quieres crear el formulario?
				</p>
				<div class="modal-buttons">
					<button @click="collectData">Si</button>
					<button @click="closeModal">No</button>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>
import Seccion from './Seccion.vue';

export default {
	inheritAttrs: false,
	components: {
		Seccion
	},
	data() {
		return {
			formDescription: '',
			charCount: 0,
			maxCharCount: 200,
			modal: false
		}
	},
	methods: {
		updateCharCount(e) {
			this.formDescription = e.target.value;
			this.charCount = this.formDescription.length;

			if (this.charCount > this.maxCharCount) {
				this.formDescription = this.formDescription.substring(0, this.maxCharCount);
				this.charCount = this.formDescription.length;
			}

			e.target.style.height = 'auto';
			e.target.style.height = (e.target.scrollHeight + 5) + 'px';
		},
		collectData() {
			// Verifica si algún campo está vacío
			if (!this.secciones) {
				alert('Debes agregar al menos una sección con preguntas');
				return;
			} else if (document.getElementById('formTitle').value === '') {
				alert('Debes agregar un título al formulario');
				return;
			} else if (this.formDescription === '') {
				alert('Debes agregar una descripción al formulario');
				return;
			}

			// Si todos los campos están completos, crea el objeto formData
			let formData = {
				types_id: document.getElementById('formType').value,
				habilitado: true,
				fechalimite: document.getElementById('formDate').value,
				preguntasdata: {
					title: document.getElementById('formTitle').value,
					description: this.formDescription,
					sections: this.secciones.map(seccion => ({
						sectionName: seccion.nombre,
						questions: seccion.preguntas.map(pregunta => {
							let answers;
							if (pregunta.tipo === 'range') {
								answers = Array.from({ length: pregunta.rango + 1 }, (_, i) => i.toString());
							} else {
								answers = pregunta.respuestas.map(respuesta => respuesta.texto);
							}
							return {
								question: pregunta.texto,
								answers: answers,
								type: pregunta.tipo
							};
						})
					}))
				}
			};
			console.log(JSON.stringify(formData));

			// Mandar el objeto formData a la API
			// fetch('http://localhost:8080/forms', {
			// 	method: 'POST',
			// 	headers: {
			// 		'Content-Type': 'application/json'
			// 	},
			// 	body: JSON.stringify(formData)
			// }).then(response => {
			// 	if (response.ok) {
			// 		alert('Formulario creado exitosamente');
			// 		this.closeModal();
			// 	} else {
			// 		alert('Hubo un error al crear el formulario');
			// 	}
			// }).catch(error => {
			// 	console.error('Error:', error);
			// });
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
h1 {
	text-align: center;
	font-size: 2rem;
	font-weight: 700;
	margin: 1rem;
}

h2 {
	text-align: center;
	font-size: 1.125rem;
}

.create {
	position: fixed;
	width: 160px;
	bottom: 1rem;
	right: 1rem;
	padding: 1rem;
	border: none;
	border-radius: 5px;
	background-color: #e5282f;
	color: #fff;
	cursor: pointer;

	transition: all 0.3s ease;
}

.create:hover {
	background-color: #ff3d42;
	box-shadow: 0 0 5px 0 #e5282f;
}

.form-container {
	width: 70%;
	height: auto;
	margin: 1rem auto 3rem auto;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	padding: 1rem;
	border: 1px solid #d3d2d2;
	border-radius: 5px;
	box-shadow: 0 0 5px 0 #d3d2d2;
	background-color: #f9f9f9;
}

.part {
	margin: 1rem 0;
	width: 100%;
}

.textarea-container {
	position: relative;
}

.char-counter {
	position: absolute;
	bottom: 10px;
	right: 10px;
}


input,
select,
textarea {
	width: 100%;
}

textarea {
	resize: none;
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

@media (max-width: 768px) {
	.form-container {
		width: 90%;
	}

	h1 {
		font-size: 1.5rem;
	}

	h2 {
		font-size: 1rem;
	}

	.part {
		margin: 0.5rem;
	}

}

@media (max-width: 425px) {
	.form-container {
		width: 95%;
	}
}
</style>