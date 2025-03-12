<template>
	<section class="form-container" id="form-container">
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
		<Seccion :secciones="secciones" @update:secciones="secciones = $event" @duplicateSection="duplicateSection" />
	</section>
	<button class="create1" @click="openModal('old')">Ver Código (Antiguo)</button>
	<button class="create2" @click="openModal('new')">Ver Código (Nuevo)</button>

	<transition name="modal">
		<div v-if="modal" class="modal">
			<div class="modal-content">
				<p>Este es el JSON del formulario que estás a punto de crear. Puedes copiarlo y pegarlo en el archivo de
					configuración de formularios.</p>
				<pre>{{ modalFormat === 'old' ? formDataJson : formDataNewJson }}</pre>
				<div class="modal-buttons">
					<button @click="closeModal">Salir</button>
					<button @click="copyFormDataJson">Copiar JSON</button>
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
		Seccion,
	},
	data() {
		return {
			formDescription: '',
			charCount: 0,
			maxCharCount: 100000,
			modal: false,
			formDataJson: '',
			formDataNewJson: '',
			modalFormat: 'old',
			secciones: []
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
			let formData = {
				title: document.getElementById('formTitle').value,
				description: this.formDescription,
				sections: this.secciones.map((seccion, sectionIndex) => ({
					sectionName: seccion.nombre,
					sectionNumber: sectionIndex + 1,
					questions: seccion.preguntas.map((pregunta, questionIndex) => {
						let answers;
						if (pregunta.tipo === 'range') {
							answers = Array.from({ length: pregunta.rango.max - pregunta.rango.min + 1 }, (_, i) => (i + pregunta.rango.min).toString());
						} else if (pregunta.tipo === 'download') {
							answers = [pregunta.download];
						}
						else if (pregunta.tipo === 'text-optional') {
							answers = pregunta.respuestas.map((respuesta, answerIndex) => ({
								answerText: respuesta.texto.trim(),
								answerNumber: answerIndex + 1
							}));
						} else {
							answers = pregunta.respuestas.map((respuesta, answerIndex) => ({
								answerText: respuesta.texto.trim(),
								answerNumber: answerIndex + 1
							}));
						}
						let questions = [
							{
								question: pregunta.texto,
								questionNumber: questionIndex + 1,
								answers: answers,
								type: pregunta.tipo === 'radio' ? 'single' :
									pregunta.tipo === 'text-optional' ? 'single' : pregunta.tipo
							}
						]
						if (pregunta.tipo === 'text-optional') {
							questions.push({
								question: pregunta.optional,
								type: 'text-optional',
							});
						}
						return questions;
					}).flat()
				}))
			};

			let formDataNew = {
				title: document.getElementById('formTitle').value,
				description: this.formDescription,
				sections: this.secciones.map((seccion, sectionIndex) => ({
					sectionName: seccion.nombre,
					sectionNumber: sectionIndex + 1,
					questions: seccion.preguntas.map((pregunta, questionIndex) => {
						let answers;
						if (pregunta.tipo === 'range') {
							answers = Array.from({ length: pregunta.rango.max - pregunta.rango.min + 1 }, (_, i) => (i + pregunta.rango.min).toString());
						} else if (pregunta.tipo === 'download') {
							answers = [pregunta.download];
						}
						else if (pregunta.tipo === 'text-optional') {
							answers = pregunta.respuestas.map((respuesta, answerIndex) => ({
								answerText: respuesta.texto.trim(),
								answerNumber: answerIndex + 1
							}));
						} else {
							answers = pregunta.respuestas.map((respuesta, answerIndex) => ({
								answerText: respuesta.texto.trim(),
								answerNumber: answerIndex + 1
							}));
						}
						let questions = [
							{
								question: pregunta.texto,
								questionNumber: questionIndex + 1,
								answers: answers,
								type: pregunta.tipo === 'radio' ? 'single' :
									pregunta.tipo === 'text-optional' ? 'single' : pregunta.tipo,
								orientation: pregunta.orientacion || 'horizontal'
							}
						]
						if (pregunta.tipo === 'text-optional') {
							questions.push({
								question: pregunta.optional,
								type: 'text-optional',
							});
						}
						return questions;
					}).flat()
				}))
			};

			this.formDataJson = JSON.stringify(formData, null, 2);
			this.formDataNewJson = JSON.stringify(formDataNew, null, 2);
		},
		openModal(format) {
			let isAnswerValid;
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
			isAnswerValid = this.verifyOptionalType();
			if (!isAnswerValid) {
				alert('Debe de haber al menos una respuesta llamada "Otro" o "No"');
				return;
			}
			this.collectData();
			this.modalFormat = format;
			this.modal = true;
		},
		closeModal() {
			this.modal = false;
		},
		copyFormDataJson() {
			navigator.clipboard.writeText(this.modalFormat === 'old' ? this.formDataJson : this.formDataNewJson);
			this.closeModal();
		},
		verifyOptionalType() {
			let isValid = false;
			this.secciones.forEach(seccion => {
				seccion.preguntas.forEach(pregunta => {
					if (pregunta.tipo === 'text-optional') {
						let hasOther = pregunta.respuestas.some(respuesta => respuesta.texto.toLowerCase().trim() === 'otro');
						let hasNo = pregunta.respuestas.some(respuesta => respuesta.texto.toLowerCase().trim() === 'no');
						if (hasOther || hasNo) {
							isValid = true;
						} else {
							isValid = false;
						}
					} else {
						isValid = true;
					}
				});
			});
			return isValid;
		},
		duplicateSection(index) {
			const seccion = this.secciones[index];
			this.secciones.splice(index + 1, 0, JSON.parse(JSON.stringify(seccion)));
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

.create1 {
	position: fixed;
	width: 160px;
	bottom: 11rem;
	right: 1rem;
	padding: 1rem;
	border: none;
	border-radius: 5px;
	background-color: #e5282f;
	color: #fff;
	cursor: pointer;

	transition: all 0.3s ease;
}

.create1:hover {
	background-color: #ff3d42;
	box-shadow: 0 0 5px 0 #e5282f;
}

.create2 {
	position: fixed;
	width: 160px;
	bottom: 5.3rem;
	right: 1rem;
	padding: 1rem;
	border: none;
	border-radius: 5px;
	background-color: #e5282f;
	color: #fff;
	cursor: pointer;

	transition: all 0.3s ease;
}

.create2:hover {
	background-color: #ff3d42;
	box-shadow: 0 0 5px 0 #e5282f;
}

.preview {
	position: fixed;
	width: 160px;
	bottom: 1rem;
	right: 1rem;
	padding: 1rem;
	border: none;
	border-radius: 5px;
	background-color: #0265b2;
	color: #fff;
	cursor: pointer;

	transition: all 0.3s ease;
}

.preview:hover {
	background-color: #0275ce;
	box-shadow: 0 0 5px 0 #0265b2;
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

pre {
	background-color: #f5f5f5;
	border: 1px solid #ccc;
	padding: 10px;
	white-space: pre-wrap;
	word-wrap: break-word;
	text-align: left;
	width: 100%;
	height: 300px;

	overflow-y: scroll;
	overflow-x: auto;
}

code {
	font-family: monospace;
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