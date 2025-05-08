<template>
  <div class="use-case-estimator" ref="estimatorContent">
    <!-- Botón para generar PDF -->
    <div class="pdf-button-container">
      <button @click="generatePDF" class="pdf-button">
        <i class="fas fa-file-pdf"></i> Generar PDF
      </button>
    </div>
    
    <h1>Estimación de Proyectos con Puntos de Casos de Uso</h1>
    
    <!-- Paso 1: Cálculo de UUCP -->
    <div class="section">
      <h2>1. Cálculo de Puntos de Casos de Uso No Ajustados (UUCP)</h2>
      
      <div class="subsection">
        <h3>Actores</h3>
        <table>
          <thead>
            <tr>
              <th>Tipo de Actor</th>
              <th>Descripción</th>
              <th>Factor</th>
              <th>Cantidad</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(actor, index) in actors" :key="'actor-'+index">
              <td>{{ actor.type }}</td>
              <td>{{ actor.description }}</td>
              <td>{{ actor.factor }}</td>
              <td><input type="number" v-model.number="actor.count" min="0"></td>
              <td>{{ actor.factor * actor.count }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="4" class="text-right"><strong>Total Actor Weight (UAW):</strong></td>
              <td><strong>{{ totalUAW }}</strong></td>
            </tr>
          </tfoot>
        </table>
      </div>
      
      <div class="subsection">
        <h3>Casos de Uso</h3>
        <table>
          <thead>
            <tr>
              <th>Tipo de Caso de Uso</th>
              <th>Descripción</th>
              <th>Factor</th>
              <th>Cantidad</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(useCase, index) in useCases" :key="'usecase-'+index">
              <td>{{ useCase.type }}</td>
              <td>{{ useCase.description }}</td>
              <td>{{ useCase.factor }}</td>
              <td><input type="number" v-model.number="useCase.count" min="0"></td>
              <td>{{ useCase.factor * useCase.count }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="4" class="text-right"><strong>Total Use Case Weight (UUCW):</strong></td>
              <td><strong>{{ totalUUCW }}</strong></td>
            </tr>
          </tfoot>
        </table>
      </div>
      
      <div class="result">
        <h3>UUCP = UAW + UUCW</h3>
        <p><strong>{{ totalUAW }} + {{ totalUUCW }} = {{ UUCP }}</strong></p>
      </div>
    </div>
    
    <!-- Paso 2: Cálculo de UCP -->
    <div class="section">
      <h2>2. Cálculo de Puntos de Casos de Uso (UCP)</h2>
      
      <div class="subsection">
        <h3>Factores Técnicos (TCF)</h3>
        <table>
          <thead>
            <tr>
              <th>Factor</th>
              <th>Descripción</th>
              <th>Peso</th>
              <th>Nivel (0-5)</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(factor, index) in technicalFactors" :key="'tech-'+index">
              <td>{{ factor.code }}</td>
              <td>{{ factor.description }}</td>
              <td>{{ factor.weight }}</td>
              <td><input type="number" v-model.number="factor.level" min="0" max="5"></td>
              <td>{{ factor.weight * factor.level }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="4" class="text-right"><strong>TFactor:</strong></td>
              <td><strong>{{ TFactor }}</strong></td>
            </tr>
            <tr>
              <td colspan="4" class="text-right"><strong>TCF = 0.6 + (0.01 × TFactor):</strong></td>
              <td><strong>{{ TCF.toFixed(3) }}</strong></td>
            </tr>
          </tfoot>
        </table>
      </div>
      
      <div class="subsection">
        <h3>Factores Ambientales (EF)</h3>
        <table>
          <thead>
            <tr>
              <th>Factor</th>
              <th>Descripción</th>
              <th>Peso</th>
              <th>Nivel (0-5)</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(factor, index) in environmentalFactors" :key="'env-'+index">
              <td>{{ factor.code }}</td>
              <td>{{ factor.description }}</td>
              <td>{{ factor.weight }}</td>
              <td><input type="number" v-model.number="factor.level" min="0" max="5"></td>
              <td>{{ factor.weight * factor.level }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="4" class="text-right"><strong>EFactor:</strong></td>
              <td><strong>{{ EFactor }}</strong></td>
            </tr>
            <tr>
              <td colspan="4" class="text-right"><strong>EF = 1.4 + (-0.03 × EFactor):</strong></td>
              <td><strong>{{ EF.toFixed(3) }}</strong></td>
            </tr>
          </tfoot>
        </table>
      </div>
      
      <div class="result">
        <h3>UCP = UUCP × TCF × EF</h3>
        <p><strong>{{ UUCP }} × {{ TCF.toFixed(3) }} × {{ EF.toFixed(3) }} = {{ UCP.toFixed(3) }}</strong></p>
      </div>
    </div>
    
    <!-- Paso 3: Estimación de Horas-Hombre -->
    <div class="section">
      <h2>3. Estimación de Horas-Hombre</h2>
      
      <div class="input-group">
        <label for="hoursPerUCP">Horas por UCP:</label>
        <input id="hoursPerUCP" type="number" v-model.number="hoursPerUCP" min="1">
        <span>(Valor sugerido: 20)</span>
      </div>
      
      <div class="input-group">
        <label for="teamSize">Tamaño del equipo:</label>
        <input id="teamSize" type="number" v-model.number="teamSize" min="1">
      </div>
      
      <div class="input-group">
        <label for="hoursPerWeek">Horas por semana por persona:</label>
        <input id="hoursPerWeek" type="number" v-model.number="hoursPerWeek" min="1">
        <span>(Valor típico: 40)</span>
      </div>
      
      <div class="result">
        <h3>Horas-Hombre Totales</h3>
        <p><strong>{{ totalManHours.toFixed(2) }} horas</strong></p>
        
        <h3>Distribución por Actividad</h3>
        <table>
          <thead>
            <tr>
              <th>Actividad</th>
              <th>Porcentaje</th>
              <th>Horas</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(activity, index) in activities" :key="'activity-'+index">
              <td>{{ activity.name }}</td>
              <td>{{ activity.percentage }}%</td>
              <td>{{ (totalManHours * activity.percentage / 100).toFixed(2) }}</td>
            </tr>
          </tbody>
        </table>
        
        <h3>Tiempo Estimado</h3>
        <p>
          <strong>Para 1 persona:</strong> {{ weeksForOnePerson.toFixed(2) }} semanas ({{ (weeksForOnePerson * hoursPerWeek).toFixed(2) }} horas)<br>
          <strong>Para {{ teamSize }} personas:</strong> {{ weeksForTeam.toFixed(2) }} semanas ({{ (weeksForTeam * hoursPerWeek * teamSize).toFixed(2) }} horas)
        </p>
      </div>
    </div>
    
    <!-- Cálculo de Costos -->
    <div class="section">
      <h2>Cálculo de Costos</h2>
      
      <div class="input-group">
        <label for="hourlyRate">Costo por hora ($):</label>
        <input id="hourlyRate" type="number" v-model.number="hourlyRate" min="0" step="0.01">
      </div>
      
      <div class="result">
        <h3>Costo Total Estimado</h3>
        <p><strong>${{ totalCost.toFixed(2) }}</strong></p>
      </div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf';

export default {
  name: 'UseCaseEstimator',
  data() {
    return {
      // Datos para cálculo de UUCP
      actors: [
        { type: 'Simple', description: 'Otro sistema con una API definida', factor: 1, count: 0 },
        { type: 'Medio', description: 'Otro sistema interactuando con protocolo o persona en modo texto', factor: 2, count: 0 },
        { type: 'Complejo', description: 'Persona interactuando con interfaz gráfica', factor: 3, count: 0 }
      ],
      useCases: [
        { type: 'Simple', description: '3 transacciones o menos', factor: 5, count: 0 },
        { type: 'Medio', description: '4 a 7 transacciones', factor: 10, count: 0 },
        { type: 'Complejo', description: 'Más de 7 transacciones', factor: 15, count: 0 }
      ],
      
      // Datos para cálculo de TCF
      technicalFactors: [
        { code: 'T1', description: 'Sistema distribuido', weight: 2, level: 0 },
        { code: 'T2', description: 'Objetivos de performance o tiempo de respuesta', weight: 1, level: 0 },
        { code: 'T3', description: 'Eficiencia del usuario final', weight: 1, level: 0 },
        { code: 'T4', description: 'Procesamiento interno complejo', weight: 1, level: 0 },
        { code: 'T5', description: 'El código debe ser reutilizable', weight: 1, level: 0 },
        { code: 'T6', description: 'Facilidad de instalación', weight: 0.5, level: 0 },
        { code: 'T7', description: 'Facilidad de uso', weight: 0.5, level: 0 },
        { code: 'T8', description: 'Portabilidad', weight: 2, level: 0 },
        { code: 'T9', description: 'Facilidad de cambio', weight: 1, level: 0 },
        { code: 'T10', description: 'Concurrencia', weight: 1, level: 0 },
        { code: 'T11', description: 'Objetivos especiales de seguridad', weight: 1, level: 0 },
        { code: 'T12', description: 'Acceso directo a terceras partes', weight: 1, level: 0 },
        { code: 'T13', description: 'Facilidades especiales de entrenamiento a usuarios', weight: 1, level: 0 }
      ],
      
      // Datos para cálculo de EF
      environmentalFactors: [
        { code: 'E1', description: 'Familiaridad con el modelo del proyecto utilizado', weight: 1.5, level: 0 },
        { code: 'E2', description: 'Experiencia en la aplicación', weight: 0.5, level: 0 },
        { code: 'E3', description: 'Experiencia en orientación a objetos', weight: 1, level: 0 },
        { code: 'E4', description: 'Capacidad del analista líder', weight: 0.5, level: 0 },
        { code: 'E5', description: 'Motivación', weight: 1, level: 0 },
        { code: 'E6', description: 'Estabilidad en los requerimientos', weight: 2, level: 0 },
        { code: 'E7', description: 'Personal de medio tiempo', weight: -1, level: 0 },
        { code: 'E8', description: 'Dificultad en el lenguaje de programación', weight: -1, level: 0 }
      ],
      
      // Configuración para cálculo de horas
      hoursPerUCP: 20,
      teamSize: 1,
      hoursPerWeek: 40,
      hourlyRate: 50,
      
      // Distribución de actividades
      activities: [
        { name: 'Análisis', percentage: 10 },
        { name: 'Diseño', percentage: 20 },
        { name: 'Programación', percentage: 40 },
        { name: 'Pruebas', percentage: 15 },
        { name: 'Sobrecarga', percentage: 15 }
      ]
    };
  },
  computed: {
    // Cálculos para UUCP
    totalUAW() {
      return this.actors.reduce((sum, actor) => sum + (actor.factor * actor.count), 0);
    },
    totalUUCW() {
      return this.useCases.reduce((sum, useCase) => sum + (useCase.factor * useCase.count), 0);
    },
    UUCP() {
      return this.totalUAW + this.totalUUCW;
    },
    
    // Cálculos para TCF
    TFactor() {
      return this.technicalFactors.reduce((sum, factor) => sum + (factor.weight * factor.level), 0);
    },
    TCF() {
      return 0.6 + (0.01 * this.TFactor);
    },
    
    // Cálculos para EF
    EFactor() {
      return this.environmentalFactors.reduce((sum, factor) => sum + (factor.weight * factor.level), 0);
    },
    EF() {
      return 1.4 + (-0.03 * this.EFactor);
    },
    
    // Cálculo de UCP
    UCP() {
      return this.UUCP * this.TCF * this.EF;
    },
    
    // Cálculos de horas y tiempo
    totalManHours() {
      return this.UCP * this.hoursPerUCP;
    },
    weeksForOnePerson() {
      return this.totalManHours / this.hoursPerWeek;
    },
    weeksForTeam() {
      return this.weeksForOnePerson / this.teamSize;
    },
    
    // Cálculo de costos
    totalCost() {
      return this.totalManHours * this.hourlyRate;
    }
  },
  methods: {
    generatePDF() {
      // Mostrar mensaje de carga
      const loading = this.$loading({
        lock: true,
        text: 'Generando PDF...',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      });

      // Capturar el contenido del componente
      const element = this.$refs.estimatorContent;
      const options = {
        scale: 2, // Aumentar la escala para mejor calidad
        useCORS: true, // Permitir imágenes externas si las hay
        logging: false, // Desactivar logs
        scrollX: 0,
        scrollY: 0
      };

      html2canvas(element, options).then(canvas => {
        // Configurar el PDF
        const imgData = canvas.toDataURL('image/png');
        const pdf = new jsPDF({
          orientation: 'portrait',
          unit: 'mm'
        });

        // Calcular dimensiones para que el PDF se ajuste al contenido
        const imgWidth = 210; // Ancho A4 en mm
        const pageHeight = 295; // Alto A4 en mm
        const imgHeight = canvas.height * imgWidth / canvas.width;
        let heightLeft = imgHeight;
        let position = 0;

        // Agregar la primera página
        pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);
        heightLeft -= pageHeight;

        // Agregar páginas adicionales si el contenido es muy largo
        while (heightLeft >= 0) {
          position = heightLeft - imgHeight;
          pdf.addPage();
          pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);
          heightLeft -= pageHeight;
        }

        // Guardar el PDF
        pdf.save('estimacion_puntos_caso_uso.pdf');
        
        // Cerrar el indicador de carga
        loading.close();
      }).catch(error => {
        console.error('Error al generar PDF:', error);
        loading.close();
        this.$message.error('Error al generar el PDF. Por favor intente nuevamente.');
      });
    }
  }
};
</script>

<style scoped>
.use-case-estimator {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.section {
  margin-bottom: 40px;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.subsection {
  margin-bottom: 30px;
}

h1, h2, h3 {
  color: #2c3e50;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
}

h2 {
  border-bottom: 2px solid #42b983;
  padding-bottom: 10px;
  margin-bottom: 20px;
}

h3 {
  margin-bottom: 15px;
  color: #42b983;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th, td {
  padding: 10px;
  border: 1px solid #ddd;
  text-align: left;
}

th {
  background-color: #42b983;
  color: white;
}

input[type="number"] {
  width: 60px;
  padding: 5px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.result {
  background-color: #e8f5e9;
  padding: 15px;
  border-radius: 4px;
  margin-top: 20px;
}

.input-group {
  margin-bottom: 15px;
}

.input-group label {
  display: inline-block;
  width: 250px;
  margin-right: 10px;
}

.input-group input {
  margin-right: 10px;
}

.text-right {
  text-align: right;
}

/* Estilos para el botón de PDF */
.pdf-button-container {
  text-align: right;
  margin-bottom: 20px;
}

.pdf-button {
  background-color: #d32f2f;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
  display: inline-flex;
  align-items: center;
}

.pdf-button:hover {
  background-color: #b71c1c;
}

.pdf-button i {
  margin-right: 8px;
}
</style>