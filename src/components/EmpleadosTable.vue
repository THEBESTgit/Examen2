<template>
    <div class="container py-4">
      <!-- Filtros y búsqueda -->
      <div class="row mb-3">
        <div class="col-md-6 mb-2">
          <input
            v-model="searchQuery"
            placeholder="Buscar por nombre"
            class="form-control"
          />
        </div>
        <div class="col-md-6 mb-2">
          <select v-model="filterTipo" class="form-control">
            <option value="">Todos</option>
            <option value="automóvil">Automóvil</option>
            <option value="moto">Moto</option>
          </select>
        </div>
      </div>
  
      <!-- Tabla de empleados -->
      <div class="table-responsive">
        <table class="table table-bordered">
          <thead class="thead-dark">
            <tr>
              <th>Nombre</th>
              <th>Área</th>
              <th>Tipo de Vehículo</th>
              <th>Placa</th>
              <th>Color</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="empleado in filteredEmpleados" :key="empleado.id">
              <td>{{ empleado.nombre }}</td>
              <td>{{ empleado.area }}</td>
              <td>{{ empleado.vehiculo.tipo }}</td>
              <td>{{ empleado.vehiculo.placa }}</td>
              <td>{{ empleado.vehiculo.color }}</td>
              <td>
                <button
                  @click="editarEmpleado(empleado)"
                  class="btn btn-sm btn-primary"
                >
                  Editar
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
  
      <!-- Formulario de la edición -->
      <div v-if="empleadoEditando" class="row justify-content-center mt-4">
        <div class="col-md-6">
          <div class="card" style="background-color: #f0f8ff;">
            <div class="card-body">
              <h3 class="card-title mb-3">Editar Empleado</h3>
              <div class="form-group mb-2">
                <label>Nombre</label>
                <input
                  v-model="empleadoEditando.nombre"
                  placeholder="Nombre"
                  class="form-control"
                />
              </div>
              <div class="form-group mb-2">
                <label>Área</label>
                <select v-model="empleadoEditando.area" class="form-control">
                  <option value="producción">Producción</option>
                  <option value="finanzas">Finanzas</option>
                  <option value="contabilidad">Contabilidad</option>
                </select>
              </div>
              <div class="form-group mb-2">
                <label>Tipo de Vehículo</label>
                <select
                  v-model="empleadoEditando.vehiculo.tipo"
                  class="form-control"
                >
                  <option value="automóvil">Automóvil</option>
                  <option value="moto">Moto</option>
                </select>
              </div>
              <div class="form-group mb-2">
                <label>Placa</label>
                <input
                  v-model="empleadoEditando.vehiculo.placa"
                  placeholder="Placa"
                  class="form-control"
                />
              </div>
              <div class="form-group mb-3">
                <label>Color</label>
                <input
                  v-model="empleadoEditando.vehiculo.color"
                  placeholder="Color"
                  class="form-control"
                />
              </div>
              <div>
                <button @click="guardarCambios" class="btn btn-success mr-2">
                  Guardar
                </button>
                <button @click="cancelarEdicion" class="btn btn-danger">
                  Cancelar
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        empleados: [],
        searchQuery: "",
        filterTipo: "",
        empleadoEditando: null,
      };
    },
    computed: {
      filteredEmpleados() {
        return this.empleados.filter((empleado) => {
          const matchesName = empleado.nombre
            .toLowerCase()
            .includes(this.searchQuery.toLowerCase());
          const matchesTipo = this.filterTipo
            ? empleado.vehiculo.tipo === this.filterTipo
            : true;
          return matchesName && matchesTipo;
        });
      },
    },
    async created() {
      const response = await fetch("http://localhost:3000/empleados");
      this.empleados = await response.json();
    },
    methods: {
      editarEmpleado(empleado) {
        this.empleadoEditando = { ...empleado };
      },
      async guardarCambios() {
        const response = await fetch(
          `http://localhost:3000/empleados/${this.empleadoEditando.id}`,
          {
            method: "PUT",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(this.empleadoEditando),
          }
        );
        if (response.ok) {
          const index = this.empleados.findIndex(
            (e) => e.id === this.empleadoEditando.id
          );
          this.empleados[index] = this.empleadoEditando;
          this.empleadoEditando = null;
        }
      },
      cancelarEdicion() {
        this.empleadoEditando = null;
      },
    },
  };
  </script>
  
  