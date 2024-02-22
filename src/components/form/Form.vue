<template>
	<section class="form-container" id="form-container">
		<div class="part">
			<label for="formType">Selecciona el tipo de formulario</label>
			<select name="formType" id="formType">
				<option value="general">Formulario General</option>
				<option value="formacion">Formulario de Formación</option>
				<option value="cultura">Formulario de Cultura</option>
				<option value="acompanamiento">Formulario de Acompañamiento</option>
				<option value="nucleo">Formulario de Núcleo</option>
			</select>
		</div>

		<div class="part">
			<label for="formTitle">Título del formulario</label>
			<input type="text" name="formTitle" id="formTitle" />
		</div>

		<div class="part">
			<label for="formDescription">Descripción del formulario</label>
			<textarea name="formDescription" id="formDescription" rows="4"></textarea>
		</div>
		<Seccion />
	</section>
	<button class="create"> Crear formulario</button>
</template>

<script>
import Seccion from './Seccion.vue';

export default {
	inheritAttrs: false,
	components: {
		Seccion
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
	margin: 0 auto;
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