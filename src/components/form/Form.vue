<template>
	<section class="form-container" id="form-container">
		<div class="part">
			<label for="formType" class="text-xl font-medium">Selecciona el tipo de formulario</label>
			<select name="formType" id="formType">
				<option value="general">Formulario General</option>
				<option value="formacion">Formulario de Formación</option>
				<option value="cultura">Formulario de Cultura</option>
				<option value="acompanamiento">Formulario de Acompañamiento</option>
				<option value="nucleo">Formulario de Núcleo</option>
			</select>
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
	<button class="create" @click="collectData"> Crear formulario</button>
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
			maxCharCount: 200
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
			};
			console.log(JSON.stringify(formData));
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
	width: 150px;
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
	margin: 1rem;
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