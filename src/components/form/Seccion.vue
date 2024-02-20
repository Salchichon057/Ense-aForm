// Seccion.vue
<template>
    <div v-for="(seccion, index) in secciones" :key="index" class="part-seccion">
        <div class="part-seccion-title">
            <label :for="'formSection' + index">Nombre de la Sección</label>
            <input type="text" :name="'formSection' + index" :id="'formSection' + index" v-model="seccion.nombre" />
        </div>
        <div class="part-seccion-content">
            <Pregunta v-for="(pregunta, indexPregunta) in seccion.preguntas" :key="indexPregunta" :pregunta="pregunta" :index="indexPregunta" @agregarRespuesta="agregarRespuesta(index)" />
        </div>
    </div>
    <button @click="agregarSeccion" class="button">
        <i class="fas fa-plus"></i>
        Agregar Sección
    </button>
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
            secciones: []
        }
    },
    methods: {
        agregarSeccion() {
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
        agregarRespuesta(indexSeccion) {
            this.secciones[indexSeccion].preguntas[0].respuestas.push({
                texto: ''
            });
        }
    }
}
</script>

<style>
.button-content {
	display: flex;
	justify-content: center;
	padding-top: 5px;
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
	display: flex;
	flex-flow: column wrap;
	justify-content: center;
	align-items: center;
	gap: 1rem;
	width: 100%;

	border: 1px solid #d3d2d2;
	border-radius: 5px;
	padding: 1rem;
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