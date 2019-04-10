<template>
  <div class="container">
    <div class="row d-none d-md-block">
      <div class="jumbotron">
        <h1 class="display-4">Calibración de Matriz de Priorización</h1>
        <p class="lead">
          Aplicación del método de referencia
          <em>Matriz semi-cuantitativa de priorización por Riesgo</em>, enmarcado en el Análisis de Criticidad del modelo de mantenimiento y confiabilidad, propuesto por el Dr. Carlos Parra.
        </p>
        <hr class="my-4">
        <p>Este ejercicio ha sido desarrollado, solo con propósitos ilustrativos de cómo se puede calibrar una matriz de esta naturaleza. En ningún caso, debe ser considerado como una aplicación.</p>
      </div>
    </div>
    <div class="row">
      <div class="col-sm">
        <nav class="mb-3">
          <div class="nav nav-tabs" id="nav-tab" role="tablist">
            <a
              class="nav-item nav-link active"
              id="nav-fi-tab"
              data-toggle="tab"
              href="#nav-fi"
              role="tab"
              aria-controls="nav-fi"
              aria-selected="true"
            >Consecuencias</a>
            <a
              class="nav-item nav-link"
              id="nav-ff-tab"
              data-toggle="tab"
              href="#nav-ff"
              role="tab"
              aria-controls="nav-ff"
              aria-selected="false"
            >Escala de FF</a>
            <a
              class="nav-item nav-link"
              id="nav-consecuencias-tab"
              data-toggle="tab"
              href="#nav-consecuencias"
              role="tab"
              aria-controls="nav-consecuencias"
              aria-selected="false"
            >Escala de Cons</a>
          </div>
        </nav>
        <div class="tab-content" id="nav-tabContent">
          <div
            class="tab-pane fade show active"
            id="nav-fi"
            role="tabpanel"
            aria-labelledby="nav-fi-tab"
          >
            <div class="row">
              <div class="col">
                <div class="form-group">
                  <label for="exampleFormControlSelect2">
                    Haga
                    <kbd>Click</kbd>, manteniendo presionada la techa
                    <kbd>Ctrl</kbd>, para seleccionar varios
                  </label>
                  <select
                    multiple
                    class="form-control"
                    id="consSelect"
                    size="10"
                    v-model="selectedCons"
                  >
                    <option v-for="(row, rowIndex) in consecuencias">{{ row }}</option>
                  </select>
                  <button
                    type="button"
                    class="btn btn-danger btn-block"
                    @click="removeConsecuencia"
                  >Eliminar</button>
                </div>
              </div>
              <div class="col mt-5">
                <div class="form-group">
                  <label for="exampleFormControlInput1">Email address</label>
                  <input
                    class="form-control"
                    type="text"
                    placeholder="Nuevo factor"
                    id="newCons"
                    v-model="newCons"
                  >
                  <button
                    type="button"
                    class="btn btn-primary btn-block mt-1"
                    @click="addConsecuencia"
                  >Agregar</button>
                </div>
              </div>
            </div>
          </div>
          <div class="tab-pane fade show" id="nav-ff" role="tabpanel" aria-labelledby="nav-ff-tab">
            <div class="form-group row">
              <label for="escalaFF" class="col-sm-2 col-form-label">Filas:</label>
              <div class="col-sm-10">
                <input
                  class="form-control"
                  type="number"
                  placeholder="¿Filas?"
                  id="escalaFF"
                  min="1"
                  v-model="filas"
                >
              </div>
            </div>
            <template v-for="(row, rowIndex) in probabilidades">
              <div class="input-group">
                <div class="input-group-prepend">
                  <span class="input-group-text">Valor y Denominación</span>
                </div>
                <input type="number" aria-label="Valor" class="form-control" v-model="row.value">
                <input
                  type="text"
                  aria-label="Denominación"
                  class="form-control"
                  v-model="row.denomination"
                >
              </div>
              <div class="input-group mb-3">
                <div class="input-group-prepend">
                  <span class="input-group-text">Descripción</span>
                </div>
                <textarea class="form-control" aria-label="Descripción" v-model="row.description"></textarea>
              </div>
            </template>
          </div>
          <div
            class="tab-pane fade show"
            id="nav-consecuencias"
            role="tabpanel"
            aria-labelledby="nav-consecuencias-tab"
          >
            <div class="form-group row">
              <label for="escalaCons" class="col-sm-2 col-form-label">Columnas:</label>
              <div class="col-sm-10">
                <input
                  class="form-control"
                  type="number"
                  placeholder="¿Columnas?"
                  id="escalaCons"
                  min="1"
                  v-model="columnas"
                >
              </div>
            </div>
            <div class="accordion" id="accordionCons">
              <div class="card" v-for="(cons, consIndex) in consecuencias">
                <div class="card-header" :id="cons">
                  <h2 class="mb-0">
                    <button
                      :class="'btn btn-link' + ((consIndex > 0) ? ' collapsed' : '')"
                      type="button"
                      data-toggle="collapse"
                      :data-target="'#collapse'+ cons"
                      :aria-expanded="(consIndex == 0) ? 'true' : 'false'"
                      :aria-controls="'collapse'+ cons"
                    >{{ cons }}</button>
                  </h2>
                </div>

                <div
                  :id="'collapse'+ cons"
                  :class="'collapse' + ((consIndex == 0) ? ' show' : '')"
                  :aria-labelledby="'heading' + cons"
                  data-parent="#accordionCons"
                >
                  <div class="card-body">
                    <template v-for="(row, rowIndex) in consEscala">
                      <div class="input-group">
                        <div class="input-group-prepend">
                          <span class="input-group-text">Valor y Denominación ({{ rowIndex + 1}})</span>
                        </div>
                        <input
                          type="number"
                          aria-label="Valor"
                          class="form-control"
                          v-model="row.value"
                        >
                        <input
                          type="text"
                          aria-label="Denominación"
                          class="form-control"
                          v-model="row.denomination"
                        >
                      </div>
                      <div class="input-group mb-3">
                        <div class="input-group-prepend">
                          <span class="input-group-text">Descripción</span>
                        </div>
                        <textarea
                          class="form-control"
                          aria-label="Descripción"
                          v-model="consValues[rowIndex][cons]"
                        ></textarea>
                      </div>
                    </template>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-sm">
        <h2>Niveles de Criticidad</h2>
        <div class="form-group row">
          <label for="criticidadMA" class="col-sm-4 col-form-label">Muy Alta:</label>
          <div class="col-sm-8">
            <input
              class="form-control"
              type="number"
              placeholder="Criticidad Muy Alta"
              id="criticidadMA"
              :min="alta+1"
              v-model="muyAlta"
            >
          </div>
        </div>
        <div class="form-group row">
          <label for="criticidadA" class="col-sm-4 col-form-label">Alta:</label>
          <div class="col-sm-8">
            <input
              class="form-control"
              type="number"
              placeholder="Criticidad Alta"
              id="criticidadA"
              :min="media+1"
              :max="muyAlta-1"
              v-model="alta"
            >
          </div>
        </div>
        <div class="form-group row">
          <label for="criticidadM" class="col-sm-4 col-form-label">Media:</label>
          <div class="col-sm-8">
            <input
              class="form-control"
              type="number"
              placeholder="Criticidad Media"
              id="criticidadM"
              :min="1"
              :max="alta-1"
              v-model="media"
            >
          </div>
        </div>

        <table class="table table-borderless table-sm">
          <caption>Matriz de Priorización por Riesgo</caption>
          <tr v-for="(row, rowIndex) in probabilidades">
            <td class="escala">{{ probabilidades.length - rowIndex }}</td>
            <td
              v-for="(col, colIndex) in consEscala"
              :class="nivelCriticidad((probabilidades.length-1) - rowIndex, col.value)+'-class'"
              data-toggle="tooltip"
              data-placement="top"
              :title="probabilidades[(probabilidades.length-1) - rowIndex].value + ' * ' + col.value + ' = ' + (probabilidades[(probabilidades.length-1) - rowIndex].value*col.value)"
            >{{ nivelCriticidad((probabilidades.length-1) - rowIndex, col.value) }}</td>
          </tr>
          <tr class="escala">
            <td></td>
            <td v-for="(col, colIndex) in arrayFilas">{{ col }}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      title: "Calibración de Matriz de Priorización por Riesgo",
      filas: 0,
      columnas: 0,
      muyAlta: 20,
      alta: 12,
      media: 5,
      probabilidades: [],
      consecuencias: ["CM", "LC", "SHA", "Imagen"],
      consEscala: [],
      consValues: [],
      newCons: "",
      selectedCons: []
    };
  },
  methods: {
    addConsecuencia() {
      if (this.newCons == "") {
        alert("Debe escribir un nombre para la consecuencia");
      } else {
        this.consecuencias.push(this.newCons);
        this.newCons = "";
      }
    },
    removeConsecuencia() {
      this.selectedCons.forEach(sc => {
        const pos = this.consecuencias.indexOf(sc);
        this.consecuencias.splice(pos, 1);
      });
      this.selectedCons = [];
    },
    nivelCriticidad: function(rowValue, colValue) {
      let ret = "B";
      let value = this.probabilidades[rowValue].value * colValue;

      if (value >= this.muyAlta) ret = "MA";
      else if (value >= this.alta) ret = "A";
      else if (value >= this.media) ret = "M";

      return ret;
    },
    reDimentionProbs(newFilas, oldFilas) {
      let paso = 0;
      let probs = 0;
      console.log(newFilas, oldFilas);

      if (newFilas < oldFilas && this.probabilidades.length > 0) {
        for (paso = newFilas; paso < oldFilas; paso++) {
          this.probabilidades.pop();
        }
        // console.log(this.probabilidades);
      } else {
        // Probabilidades
        for (paso = 0; paso < this.filas; paso++) {
          if (!this.probabilidades[paso]) {
            let probObj = {};
            probObj.value = paso + 1;
            switch (paso) {
              case 0:
                probObj.denomination = "Muy Bajo";
                break;
              case 1:
                probObj.denomination = "Bajo";
                break;
              case 2:
                probObj.denomination = "Moderado";
                break;
              case 3:
                probObj.denomination = "Alto";
                break;
              case 4:
                probObj.denomination = "Muy Alto";
                break;
              default:
                probObj.denomination = "¿?";
            }
            //probObj.denomination = "¿?";
            probObj.description = "¿?";

            this.probabilidades.push(probObj);
          }
        }
        //console.log(this.probabilidades);
      }
    },
    reDimentionCons(newColumnas, oldColumnas) {
      let paso = 0;
      let cons = 0;

      if (newColumnas < oldColumnas && this.consEscala.length > 0) {
        for (paso = newColumnas; paso < oldColumnas; paso++) {
          this.consValues.pop();
          this.consEscala.pop();
        }
        console.log(this.consEscala);
        console.log(this.consValues);
      } else {
        for (paso = 0; paso < newColumnas; paso++) {
          if (!this.consEscala[paso]) {
            let probObj = {};
            probObj.value = paso + 1;
            switch (paso) {
              case 0:
                probObj.denomination = "Muy Bajo";
                break;
              case 1:
                probObj.denomination = "Bajo";
                break;
              case 2:
                probObj.denomination = "Moderado";
                break;
              case 3:
                probObj.denomination = "Alto";
                break;
              case 4:
                probObj.denomination = "Muy Alto";
                break;
              default:
                probObj.denomination = "¿?";
            }
            //probObj.denomination = "¿?";

            this.consEscala.push(probObj);
          }

          if (!this.consValues[paso]) {
            let factores = {};
            for (cons = 0; cons < this.consecuencias.length; cons++) {
              this.$set(factores, this.consecuencias[cons], "¿?");
            }
            this.consValues.push(factores);
          }
        }
        console.log(this.consEscala);
        console.log(this.consValues);
      }
    }
  },
  created() {
    this.filas = 5;
    this.columnas = 5;
  },
  /* updated() {
    console.log($('[data-toggle="tooltip"]'));
    $('[data-toggle="tooltip"]').tooltip();
    console.log("pasó en componente");
  }, */
  computed: {
    getColSpan() {
      return 3 * this.consecuencias.length;
    },
    arrayFilas() {
      let ret = [];
      for (let i = 1; i <= this.columnas; i++) {
        ret.push(i);
      }

      return ret;
    }
  },
  watch: {
    filas: function(newFilas, oldFilas) {
      console.log(`filas changing from ${oldFilas} to ${newFilas}`);
      this.reDimentionProbs(newFilas, oldFilas);
    },
    columnas: function(newColumnas, oldColumnas) {
      console.log(`columnas changing from ${oldColumnas} to ${newColumnas}`);
      this.reDimentionCons(newColumnas, oldColumnas);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.escala {
  background: #000;
  color: #fff;
}
.celda {
  /*padding: 10px;*/
}
.MA-class {
  background: red;
  color: white;
}
.A-class {
  background: green;
  color: white;
}
.M-class {
  background: yellow;
}
</style>
