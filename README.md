<!DOCTYPE html>

<html lang="es">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Sistema de Gestión de Alumnos y Cupos</title>

  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">

  <!-- CDN para SheetJS (librería para exportar a Excel) -->

  <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>

  <style>

    :root {

      --primary-color: #6f42c1; /* Morado */

      --secondary-color: #6c757d; /* Gris */

      --success-color: #28a745; /* Verde */

      --info-color: #17a2b8; /* Azul claro */

      --warning-color: #ffc107; /* Amarillo */

      --danger-color: #dc3545; /* Rojo */

      --light-bg: #f8f9fa;

      --dark-text: #343a40;

      --card-bg: #ffffff;

    }



    body {

      font-family: 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;

      background-color: var(--light-bg);

      color: var(--dark-text);

      line-height: 1.6;

    }



    .container {

      max-width: 1200px;

      margin-top: 30px;

      margin-bottom: 30px;

    }



    h1 {

      color: var(--primary-color);

      font-weight: 700;

      margin-bottom: 30px;

      text-align: center;

      position: relative;

    }

    h1::after {

      content: '';

      display: block;

      width: 80px;

      height: 4px;

      background-color: var(--primary-color);

      margin: 15px auto 0;

      border-radius: 2px;

    }



    .card {

      border-radius: 15px;

      box-shadow: 0 10px 25px rgba(0,0,0,0.1);

      border: none;

      background-color: var(--card-bg);

      margin-bottom: 25px;

    }



    .card-header {

      border-radius: 15px 15px 0 0 !important;

      font-size: 1.35rem;

      font-weight: 600;

      padding: 1.25rem 1.5rem;

      color: white;

      background-color: var(--primary-color); /* Default header color */

      border-bottom: none;

    }

    .card-header.bg-primary { background-color: var(--primary-color) !important; }

    .card-header.bg-success { background-color: var(--success-color) !important; }

    .card-header.bg-info { background-color: var(--info-color) !important; }

    .card-header.bg-secondary { background-color: var(--secondary-color) !important; }





    .nav-tabs {

      border-bottom: 2px solid var(--primary-color);

      margin-bottom: 25px;

    }

    .nav-tabs .nav-link {

      color: var(--dark-text);

      border: none;

      border-bottom: 3px solid transparent;

      padding: 12px 20px;

      font-weight: 500;

      transition: all 0.3s ease;

    }

    .nav-tabs .nav-link:hover {

      color: var(--primary-color);

      border-color: var(--primary-color);

    }

    .nav-tabs .nav-link.active {

      color: var(--primary-color);

      background-color: transparent;

      border-color: var(--primary-color);

      font-weight: 600;

    }



    .form-group-grid {

      display: grid;

      grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));

      gap: 1.5rem;

    }

    .form-group-full {

      grid-column: 1 / -1;

    }



    .form-control, .form-select {

      border-radius: 8px;

      border: 1px solid #ced4da;

      padding: 0.75rem 1rem;

      transition: all 0.3s ease;

    }

    .form-control:focus, .form-select:focus {

      border-color: var(--primary-color);

      box-shadow: 0 0 0 0.25rem rgba(111, 66, 193, 0.25); /* primary-color with alpha */

    }



    .btn {

      border-radius: 8px;

      padding: 0.75rem 1.5rem;

      font-weight: 600;

      transition: all 0.3s ease;

    }

    .btn-primary { background-color: var(--primary-color); border-color: var(--primary-color); }

    .btn-primary:hover { background-color: #5a36a3; border-color: #5a36a3; } /* Darker primary */

    .btn-info { background-color: var(--info-color); border-color: var(--info-color); }

    .btn-info:hover { background-color: #138496; border-color: #138496; } /* Darker info */

    .btn-warning { background-color: var(--warning-color); border-color: var(--warning-color); color: var(--dark-text); }

    .btn-warning:hover { background-color: #e0a800; border-color: #e0a800; }

    .btn-danger { background-color: var(--danger-color); border-color: var(--danger-color); }

    .btn-danger:hover { background-color: #bd2130; border-color: #bd2130; }

    .btn-secondary { background-color: var(--secondary-color); border-color: var(--secondary-color); }

    .btn-secondary:hover { background-color: #545b62; border-color: #545b62; }



    .table {

      margin-top: 15px;

      border-radius: 10px;

      overflow: hidden; /* Ensures rounded corners for table */

    }

    .table thead th {

      background-color: var(--primary-color);

      color: white;

      border-bottom: none;

      padding: 1rem;

    }

    .table tbody tr:nth-of-type(odd) {

      background-color: rgba(0,0,0,.03);

    }

    .table tbody tr:hover {

      background-color: rgba(111, 66, 193, 0.1); /* Light hover effect */

    }

    .table td, .table th {

      vertical-align: middle;

      border-top: 1px solid #dee2e6;

      padding: 0.85rem;

    }



    /* Autocomplete styles */

    .autocomplete-items {

      position: absolute;

      border: 1px solid #d4d4d4;

      border-bottom: none;

      border-top: none;

      z-index: 99;

      top: 100%;

      left: 0;

      right: 0;

      max-height: 200px;

      overflow-y: auto;

      background-color: var(--card-bg);

      border-radius: 0 0 8px 8px;

      box-shadow: 0 4px 10px rgba(0,0,0,0.1);

    }

    .autocomplete-items div {

      padding: 10px;

      cursor: pointer;

      background-color: var(--card-bg);

      border-bottom: 1px solid #e9ecef;

      transition: background-color 0.2s ease;

    }

    .autocomplete-items div:hover {

      background-color: #e9e9e9;

    }

    .autocomplete-active {

      background-color: var(--primary-color) !important;

      color: #ffffff;

    }

    .autocomplete-active strong {

      color: #ffffff;

    }



    /* Estilos para las fechas seleccionadas */

    .fecha-tag {

      display: inline-block;

      background-color: var(--primary-color);

      color: white;

      padding: 5px 10px;

      border-radius: 5px;

      margin: 5px;

      font-size: 0.9em;

    }

    .fecha-tag .btn-close-tag {

      background: none;

      border: none;

      color: white;

      font-size: 0.8em;

      margin-left: 5px;

      cursor: pointer;

    }



    /* Estilos para los toasts */

    .toast-container {

      position: fixed;

      top: 1rem;

      right: 1rem;

      z-index: 1080; /* Higher than modals */

    }

    .toast {

      border-radius: 0.5rem;

      box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);

      border: none;

    }

    .toast-header {

      border-bottom: none;

      font-weight: 600;

      color: white;

      padding: 0.75rem 1rem;

      border-radius: 0.5rem 0.5rem 0 0;

    }

    .toast-body {

      padding: 1rem;

      color: var(--dark-text);

    }

    .toast.bg-success .toast-header { background-color: var(--success-color); }

    .toast.bg-danger .toast-header { background-color: var(--danger-color); }

    .toast.bg-info .toast-header { background-color: var(--info-color); }

    .toast.bg-warning .toast-header { background-color: var(--warning-color); color: var(--dark-text); }

    .toast-header .btn-close {

      filter: invert(1) grayscale(100%) brightness(200%); /* Make close button white */

    }



    /* Ajuste para los select de Pago y Kit en el modal */

    #modalAsignarProyecto .form-group-grid > div {

      flex: 1; /* Distribute space evenly */

      min-width: unset; /* Override min-width for smaller selects */

    }

    #modalAsignarProyecto .form-select {

      padding: 0.5rem 0.75rem; /* Make selects smaller */

      font-size: 0.9rem;

    }

  </style>

</head>

<body>

  <div class="container">

    <h1><i class="fas fa-graduation-cap me-2"></i>Sistema de Gestión de Alumnos y Cupos</h1>



    <ul class="nav nav-tabs mb-4" id="myTab" role="tablist">

      <li class="nav-item" role="presentation">

        <button class="nav-link active" id="registro-tab" data-bs-toggle="tab" data-bs-target="#registro" type="button" role="tab" aria-controls="registro" aria-selected="true">

          <i class="fas fa-user-plus me-2"></i>Registro de Alumnos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="lista-tab" data-bs-toggle="tab" data-bs-target="#lista" type="button" role="tab" aria-controls="lista" aria-selected="false">

          <i class="fas fa-users me-2"></i>Alumnos Inscritos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="plantilla-tab" data-bs-toggle="tab" data-bs-target="#plantilla" type="button" role="tab" aria-controls="plantilla" aria-selected="false">

          <i class="fas fa-address-book me-2"></i>Alumnos Registrados

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="cupos-tab" data-bs-toggle="tab" data-bs-target="#cupos" type="button" role="tab" aria-controls="cupos" aria-selected="false">

          <i class="fas fa-calendar-alt me-2"></i>Cupos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="profesores-tab" data-bs-toggle="tab" data-bs-target="#profesores" type="button" role="tab" aria-controls="profesores" aria-selected="false">

          <i class="fas fa-chalkboard-teacher me-2"></i>Profesores

        </button>

      </li>

      <!-- Nueva pestaña para Proyectos -->

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="proyectos-tab" data-bs-toggle="tab" data-bs-target="#proyectos" type="button" role="tab" aria-controls="proyectos" aria-selected="false">

          <i class="fas fa-project-diagram me-2"></i>Proyectos

        </button>

      </li>

      <!-- Nueva pestaña para Reportes -->

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="reportes-tab" data-bs-toggle="tab" data-bs-target="#reportes" type="button" role="tab" aria-controls="reportes" aria-selected="false">

          <i class="fas fa-chart-bar me-2"></i>Reportes

        </button>

      </li>

    </ul>



    <div class="tab-content" id="myTabContent">

      <!-- Registro de Alumnos -->

      <div class="tab-pane fade show active" id="registro" role="tabpanel" aria-labelledby="registro-tab">

        <div class="card">

          <div class="card-header bg-primary text-white"><i class="fas fa-user-plus me-2"></i>Registro de Alumnos</div>

          <div class="card-body">

            <form id="alumnoForm">

              <input type="hidden" id="idAlumnoEditando">

              <input type="hidden" id="idAlumnoPlantilla">



              <div class="mb-4">

                <label for="buscarAlumno" class="form-label fw-bold">Buscar Alumno Existente para Reinscripción</label>

                <div class="position-relative">

                  <input type="text" class="form-control" id="buscarAlumno" placeholder="Buscar por nombre o correo electrónico">

                  <div id="autocompleteList" class="autocomplete-items"></div>

                </div>

                <small class="form-text text-muted">Empieza a escribir para buscar alumnos registrados previamente.</small>

              </div>



              <div class="form-group-grid">

                <div><label for="nombre" class="form-label">Nombre Completo</label><input type="text" class="form-control" id="nombre" required></div>

                <div><label for="nivel" class="form-label">Nivel</label>

                  <select class="form-select" id="nivel" required>

                    <option value="">Seleccione</option>

                    <option value="De cero">De cero</option>

                    <option value="Basico">Básico</option>

                    <option value="Intermedio">Intermedio</option>

                    <option value="Avanzado">Avanzado</option>

                  </select>

                </div>

                <div><label for="telefono" class="form-label">Teléfono</label><input type="tel" class="form-control" id="telefono" required></div>

                <div><label for="correo" class="form-label">Correo Electrónico</label><input type="email" class="form-control" id="correo" required></div>

                <div><label for="estatus" class="form-label">Estatus</label>

                  <select class="form-select" id="estatus" required>

                    <option value="">Seleccione</option>

                    <option value="Recurrente">Recurrente</option>

                    <option value="Nuevo">Nuevo</option>

                    <option value="Ya no asiste">Ya no asiste</option>

                  </select>

                </div>

                <!-- El select de horario se ha movido al modal de "Alumnos Inscritos" -->

              </div>

              <div class="d-grid gap-2 d-md-flex justify-content-md-end mt-4">

                <button type="submit" class="btn btn-primary" id="btnRegistrarAlumno"><i class="fas fa-save me-2"></i>Registrar Alumno</button>

                <button type="button" class="btn btn-secondary" id="btnLimpiarAlumnoForm"><i class="fas fa-eraser me-2"></i>Limpiar Formulario</button>

              </div>

            </form>

          </div>

        </div>

      </div>



      <!-- Lista de Alumnos Inscritos (Activos en un cupo) -->

      <div class="tab-pane fade" id="lista" role="tabpanel" aria-labelledby="lista-tab">

        <div class="card">

          <div class="card-header bg-success text-white"><i class="fas fa-users me-2"></i>Alumnos Inscritos (Activos en un Cupo)</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Nombre</th>

                    <th>Nivel</th>

                    <th>Horario</th>

                    <th>Proyecto Asignado</th>

                    <th>Pago</th> <!-- Nueva columna -->

                    <th>Kit</th> <!-- Nueva columna -->

                    <th>Observaciones</th>

                    <th>Estatus</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaAlumnos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Lista de Alumnos Registrados (Plantilla) -->

      <div class="tab-pane fade" id="plantilla" role="tabpanel" aria-labelledby="plantilla-tab">

        <div class="card">

          <div class="card-header bg-secondary text-white"><i class="fas fa-address-book me-2"></i>Alumnos Registrados (Plantilla)</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Nombre</th>

                    <th>Correo</th>

                    <th>Teléfono</th>

                    <th>Nivel Preferido</th>

                    <th>Observaciones</th>

                    <th>Estado</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaPlantillaAlumnos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Cupos -->

      <div class="tab-pane fade" id="cupos" role="tabpanel" aria-labelledby="cupos-tab">

        <div class="card mb-4">

          <div class="card-header bg-primary text-white"><i class="fas fa-calendar-plus me-2"></i>Registro de Nuevos Cupos</div>

          <div class="card-body">

            <form id="cupoForm">

              <div class="form-group-grid">

                <div><label for="profesorSelect" class="form-label">Profesor</label>

                  <select class="form-select" id="profesorSelect" required>

                    <option value="">Seleccione un profesor</option>

                  </select>

                </div>

                <div>

                  <label for="fechaCupoIndividual" class="form-label">Añadir Fechas</label>

                  <div class="input-group">

                    <input type="date" class="form-control" id="fechaCupoIndividual">

                    <button class="btn btn-outline-secondary" type="button" id="btnAddFechaCupo"><i class="fas fa-plus"></i> Añadir</button>

                  </div>

                  <div id="fechasSeleccionadasContainer" class="mt-2">

                    <!-- Aquí se mostrarán las fechas seleccionadas -->

                  </div>

                  <input type="hidden" id="fechasCupoHidden" required> <!-- Campo oculto para el submit -->

                </div>

                <div><label for="horaInicio" class="form-label">Hora Inicio</label><input type="time" class="form-control" id="horaInicio" required></div>

                <div><label for="horaFin" class="form-label">Hora Fin</label><input type="time" class="form-control" id="horaFin" required></div>

                <div><label for="cupoMax" class="form-label">Cupo (Máx. Personas)</label><input type="number" class="form-control" id="cupoMax" required min="1"></div>

              </div>

              <div class="d-grid mt-4">

                <button type="submit" class="btn btn-primary"><i class="fas fa-plus-circle me-2"></i>Registrar Cupo</button>

              </div>

            </form>

          </div>

        </div>



        <div class="card">

          <div class="card-header bg-success text-white"><i class="fas fa-list-alt me-2"></i>Lista de Cupos Disponibles</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Profesor</th>

                    <th>Fechas</th> <!-- Cambiado a Fechas -->

                    <th>Horario</th>

                    <th>Cupo Disponible</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaCupos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Profesores -->

      <div class="tab-pane fade" id="profesores" role="tabpanel" aria-labelledby="profesores-tab">

        <div class="card mb-4">

          <div class="card-header bg-info text-white"><i class="fas fa-user-tie me-2"></i>Registro de Profesores</div>

          <div class="card-body">

            <form id="profesorForm">

              <input type="hidden" id="idProfesorEditando">

              <div class="form-group-grid">

                <div><label for="nombreProfesor" class="form-label">Nombre Completo</label><input type="text" class="form-control" id="nombreProfesor" required></div>

                <div><label for="correoProfesor" class="form-label">Correo Electrónico</label><input type="email" class="form-control" id="correoProfesor" required></div>

                <div><label for="telefonoProfesor" class="form-label">Teléfono</label><input type="tel" class="form-control" id="telefonoProfesor"></div>

              </div>

              <div class="d-grid mt-4">

                <button type="submit" class="btn btn-info" id="btnRegistrarProfesor"><i class="fas fa-user-plus me-2"></i>Registrar Profesor</button>

              </div>

            </form>

          </div>

        </div<!DOCTYPE html>

<html lang="es">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Sistema de Gestión de Alumnos y Cupos</title>

  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">

  <!-- CDN para SheetJS (librería para exportar a Excel) -->

  <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>

  <style>

    :root {

      --primary-color: #6f42c1; /* Morado */

      --secondary-color: #6c757d; /* Gris */

      --success-color: #28a745; /* Verde */

      --info-color: #17a2b8; /* Azul claro */

      --warning-color: #ffc107; /* Amarillo */

      --danger-color: #dc3545; /* Rojo */

      --light-bg: #f8f9fa;

      --dark-text: #343a40;

      --card-bg: #ffffff;

    }



    body {

      font-family: 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;

      background-color: var(--light-bg);

      color: var(--dark-text);

      line-height: 1.6;

    }



    .container {

      max-width: 1200px;

      margin-top: 30px;

      margin-bottom: 30px;

    }



    h1 {

      color: var(--primary-color);

      font-weight: 700;

      margin-bottom: 30px;

      text-align: center;

      position: relative;

    }

    h1::after {

      content: '';

      display: block;

      width: 80px;

      height: 4px;

      background-color: var(--primary-color);

      margin: 15px auto 0;

      border-radius: 2px;

    }



    .card {

      border-radius: 15px;

      box-shadow: 0 10px 25px rgba(0,0,0,0.1);

      border: none;

      background-color: var(--card-bg);

      margin-bottom: 25px;

    }



    .card-header {

      border-radius: 15px 15px 0 0 !important;

      font-size: 1.35rem;

      font-weight: 600;

      padding: 1.25rem 1.5rem;

      color: white;

      background-color: var(--primary-color); /* Default header color */

      border-bottom: none;

    }

    .card-header.bg-primary { background-color: var(--primary-color) !important; }

    .card-header.bg-success { background-color: var(--success-color) !important; }

    .card-header.bg-info { background-color: var(--info-color) !important; }

    .card-header.bg-secondary { background-color: var(--secondary-color) !important; }





    .nav-tabs {

      border-bottom: 2px solid var(--primary-color);

      margin-bottom: 25px;

    }

    .nav-tabs .nav-link {

      color: var(--dark-text);

      border: none;

      border-bottom: 3px solid transparent;

      padding: 12px 20px;

      font-weight: 500;

      transition: all 0.3s ease;

    }

    .nav-tabs .nav-link:hover {

      color: var(--primary-color);

      border-color: var(--primary-color);

    }

    .nav-tabs .nav-link.active {

      color: var(--primary-color);

      background-color: transparent;

      border-color: var(--primary-color);

      font-weight: 600;

    }



    .form-group-grid {

      display: grid;

      grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));

      gap: 1.5rem;

    }

    .form-group-full {

      grid-column: 1 / -1;

    }



    .form-control, .form-select {

      border-radius: 8px;

      border: 1px solid #ced4da;

      padding: 0.75rem 1rem;

      transition: all 0.3s ease;

    }

    .form-control:focus, .form-select:focus {

      border-color: var(--primary-color);

      box-shadow: 0 0 0 0.25rem rgba(111, 66, 193, 0.25); /* primary-color with alpha */

    }



    .btn {

      border-radius: 8px;

      padding: 0.75rem 1.5rem;

      font-weight: 600;

      transition: all 0.3s ease;

    }

    .btn-primary { background-color: var(--primary-color); border-color: var(--primary-color); }

    .btn-primary:hover { background-color: #5a36a3; border-color: #5a36a3; } /* Darker primary */

    .btn-info { background-color: var(--info-color); border-color: var(--info-color); }

    .btn-info:hover { background-color: #138496; border-color: #138496; } /* Darker info */

    .btn-warning { background-color: var(--warning-color); border-color: var(--warning-color); color: var(--dark-text); }

    .btn-warning:hover { background-color: #e0a800; border-color: #e0a800; }

    .btn-danger { background-color: var(--danger-color); border-color: var(--danger-color); }

    .btn-danger:hover { background-color: #bd2130; border-color: #bd2130; }

    .btn-secondary { background-color: var(--secondary-color); border-color: var(--secondary-color); }

    .btn-secondary:hover { background-color: #545b62; border-color: #545b62; }



    .table {

      margin-top: 15px;

      border-radius: 10px;

      overflow: hidden; /* Ensures rounded corners for table */

    }

    .table thead th {

      background-color: var(--primary-color);

      color: white;

      border-bottom: none;

      padding: 1rem;

    }

    .table tbody tr:nth-of-type(odd) {

      background-color: rgba(0,0,0,.03);

    }

    .table tbody tr:hover {

      background-color: rgba(111, 66, 193, 0.1); /* Light hover effect */

    }

    .table td, .table th {

      vertical-align: middle;

      border-top: 1px solid #dee2e6;

      padding: 0.85rem;

    }



    /* Autocomplete styles */

    .autocomplete-items {

      position: absolute;

      border: 1px solid #d4d4d4;

      border-bottom: none;

      border-top: none;

      z-index: 99;

      top: 100%;

      left: 0;

      right: 0;

      max-height: 200px;

      overflow-y: auto;

      background-color: var(--card-bg);

      border-radius: 0 0 8px 8px;

      box-shadow: 0 4px 10px rgba(0,0,0,0.1);

    }

    .autocomplete-items div {

      padding: 10px;

      cursor: pointer;

      background-color: var(--card-bg);

      border-bottom: 1px solid #e9ecef;

      transition: background-color 0.2s ease;

    }

    .autocomplete-items div:hover {

      background-color: #e9e9e9;

    }

    .autocomplete-active {

      background-color: var(--primary-color) !important;

      color: #ffffff;

    }

    .autocomplete-active strong {

      color: #ffffff;

    }



    /* Estilos para las fechas seleccionadas */

    .fecha-tag {

      display: inline-block;

      background-color: var(--primary-color);

      color: white;

      padding: 5px 10px;

      border-radius: 5px;

      margin: 5px;

      font-size: 0.9em;

    }

    .fecha-tag .btn-close-tag {

      background: none;

      border: none;

      color: white;

      font-size: 0.8em;

      margin-left: 5px;

      cursor: pointer;

    }



    /* Estilos para los toasts */

    .toast-container {

      position: fixed;

      top: 1rem;

      right: 1rem;

      z-index: 1080; /* Higher than modals */

    }

    .toast {

      border-radius: 0.5rem;

      box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);

      border: none;

    }

    .toast-header {

      border-bottom: none;

      font-weight: 600;

      color: white;

      padding: 0.75rem 1rem;

      border-radius: 0.5rem 0.5rem 0 0;

    }

    .toast-body {

      padding: 1rem;

      color: var(--dark-text);

    }

    .toast.bg-success .toast-header { background-color: var(--success-color); }

    .toast.bg-danger .toast-header { background-color: var(--danger-color); }

    .toast.bg-info .toast-header { background-color: var(--info-color); }

    .toast.bg-warning .toast-header { background-color: var(--warning-color); color: var(--dark-text); }

    .toast-header .btn-close {

      filter: invert(1) grayscale(100%) brightness(200%); /* Make close button white */

    }



    /* Ajuste para los select de Pago y Kit en el modal */

    #modalAsignarProyecto .form-group-grid > div {

      flex: 1; /* Distribute space evenly */

      min-width: unset; /* Override min-width for smaller selects */

    }

    #modalAsignarProyecto .form-select {

      padding: 0.5rem 0.75rem; /* Make selects smaller */

      font-size: 0.9rem;

    }

  </style>

</head>

<body>

  <div class="container">

    <h1><i class="fas fa-graduation-cap me-2"></i>Sistema de Gestión de Alumnos y Cupos</h1>



    <ul class="nav nav-tabs mb-4" id="myTab" role="tablist">

      <li class="nav-item" role="presentation">

        <button class="nav-link active" id="registro-tab" data-bs-toggle="tab" data-bs-target="#registro" type="button" role="tab" aria-controls="registro" aria-selected="true">

          <i class="fas fa-user-plus me-2"></i>Registro de Alumnos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="lista-tab" data-bs-toggle="tab" data-bs-target="#lista" type="button" role="tab" aria-controls="lista" aria-selected="false">

          <i class="fas fa-users me-2"></i>Alumnos Inscritos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="plantilla-tab" data-bs-toggle="tab" data-bs-target="#plantilla" type="button" role="tab" aria-controls="plantilla" aria-selected="false">

          <i class="fas fa-address-book me-2"></i>Alumnos Registrados

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="cupos-tab" data-bs-toggle="tab" data-bs-target="#cupos" type="button" role="tab" aria-controls="cupos" aria-selected="false">

          <i class="fas fa-calendar-alt me-2"></i>Cupos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="profesores-tab" data-bs-toggle="tab" data-bs-target="#profesores" type="button" role="tab" aria-controls="profesores" aria-selected="false">

          <i class="fas fa-chalkboard-teacher me-2"></i>Profesores

        </button>

      </li>

      <!-- Nueva pestaña para Proyectos -->

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="proyectos-tab" data-bs-toggle="tab" data-bs-target="#proyectos" type="button" role="tab" aria-controls="proyectos" aria-selected="false">

          <i class="fas fa-project-diagram me-2"></i>Proyectos

        </button>

      </li>

      <!-- Nueva pestaña para Reportes -->

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="reportes-tab" data-bs-toggle="tab" data-bs-target="#reportes" type="button" role="tab" aria-controls="reportes" aria-selected="false">

          <i class="fas fa-chart-bar me-2"></i>Reportes

        </button>

      </li>

    </ul>



    <div class="tab-content" id="myTabContent">

      <!-- Registro de Alumnos -->

      <div class="tab-pane fade show active" id="registro" role="tabpanel" aria-labelledby="registro-tab">

        <div class="card">

          <div class="card-header bg-primary text-white"><i class="fas fa-user-plus me-2"></i>Registro de Alumnos</div>

          <div class="card-body">

            <form id="alumnoForm">

              <input type="hidden" id="idAlumnoEditando">

              <input type="hidden" id="idAlumnoPlantilla">



              <div class="mb-4">

                <label for="buscarAlumno" class="form-label fw-bold">Buscar Alumno Existente para Reinscripción</label>

                <div class="position-relative">

                  <input type="text" class="form-control" id="buscarAlumno" placeholder="Buscar por nombre o correo electrónico">

                  <div id="autocompleteList" class="autocomplete-items"></div>

                </div>

                <small class="form-text text-muted">Empieza a escribir para buscar alumnos registrados previamente.</small>

              </div>



              <div class="form-group-grid">

                <div><label for="nombre" class="form-label">Nombre Completo</label><input type="text" class="form-control" id="nombre" required></div>

                <div><label for="nivel" class="form-label">Nivel</label>

                  <select class="form-select" id="nivel" required>

                    <option value="">Seleccione</option>

                    <option value="De cero">De cero</option>

                    <option value="Basico">Básico</option>

                    <option value="Intermedio">Intermedio</option>

                    <option value="Avanzado">Avanzado</option>

                  </select>

                </div>

                <div><label for="telefono" class="form-label">Teléfono</label><input type="tel" class="form-control" id="telefono" required></div>

                <div><label for="correo" class="form-label">Correo Electrónico</label><input type="email" class="form-control" id="correo" required></div>

                <div><label for="estatus" class="form-label">Estatus</label>

                  <select class="form-select" id="estatus" required>

                    <option value="">Seleccione</option>

                    <option value="Recurrente">Recurrente</option>

                    <option value="Nuevo">Nuevo</option>

                    <option value="Ya no asiste">Ya no asiste</option>

                  </select>

                </div>

                <!-- El select de horario se ha movido al modal de "Alumnos Inscritos" -->

              </div>

              <div class="d-grid gap-2 d-md-flex justify-content-md-end mt-4">

                <button type="submit" class="btn btn-primary" id="btnRegistrarAlumno"><i class="fas fa-save me-2"></i>Registrar Alumno</button>

                <button type="button" class="btn btn-secondary" id="btnLimpiarAlumnoForm"><i class="fas fa-eraser me-2"></i>Limpiar Formulario</button>

              </div>

            </form>

          </div>

        </div>

      </div>



      <!-- Lista de Alumnos Inscritos (Activos en un cupo) -->

      <div class="tab-pane fade" id="lista" role="tabpanel" aria-labelledby="lista-tab">

        <div class="card">

          <div class="card-header bg-success text-white"><i class="fas fa-users me-2"></i>Alumnos Inscritos (Activos en un Cupo)</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Nombre</th>

                    <th>Nivel</th>

                    <th>Horario</th>

                    <th>Proyecto Asignado</th>

                    <th>Pago</th> <!-- Nueva columna -->

                    <th>Kit</th> <!-- Nueva columna -->

                    <th>Observaciones</th>

                    <th>Estatus</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaAlumnos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Lista de Alumnos Registrados (Plantilla) -->

      <div class="tab-pane fade" id="plantilla" role="tabpanel" aria-labelledby="plantilla-tab">

        <div class="card">

          <div class="card-header bg-secondary text-white"><i class="fas fa-address-book me-2"></i>Alumnos Registrados (Plantilla)</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Nombre</th>

                    <th>Correo</th>

                    <th>Teléfono</th>

                    <th>Nivel Preferido</th>

                    <th>Observaciones</th>

                    <th>Estado</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaPlantillaAlumnos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Cupos -->

      <div class="tab-pane fade" id="cupos" role="tabpanel" aria-labelledby="cupos-tab">

        <div class="card mb-4">

          <div class="card-header bg-primary text-white"><i class="fas fa-calendar-plus me-2"></i>Registro de Nuevos Cupos</div>

          <div class="card-body">

            <form id="cupoForm">

              <div class="form-group-grid">

                <div><label for="profesorSelect" class="form-label">Profesor</label>

                  <select class="form-select" id="profesorSelect" required>

                    <option value="">Seleccione un profesor</option>

                  </select>

                </div>

                <div>

                  <label for="fechaCupoIndividual" class="form-label">Añadir Fechas</label>

                  <div class="input-group">

                    <input type="date" class="form-control" id="fechaCupoIndividual">

                    <button class="btn btn-outline-secondary" type="button" id="btnAddFechaCupo"><i class="fas fa-plus"></i> Añadir</button>

                  </div>

                  <div id="fechasSeleccionadasContainer" class="mt-2">

                    <!-- Aquí se mostrarán las fechas seleccionadas -->

                  </div>

                  <input type="hidden" id="fechasCupoHidden" required> <!-- Campo oculto para el submit -->

                </div>

                <div><label for="horaInicio" class="form-label">Hora Inicio</label><input type="time" class="form-control" id="horaInicio" required></div>

                <div><label for="horaFin" class="form-label">Hora Fin</label><input type="time" class="form-control" id="horaFin" required></div>

                <div><label for="cupoMax" class="form-label">Cupo (Máx. Personas)</label><input type="number" class="form-control" id="cupoMax" required min="1"></div>

              </div>

              <div class="d-grid mt-4">

                <button type="submit" class="btn btn-primary"><i class="fas fa-plus-circle me-2"></i>Registrar Cupo</button>

              </div>

            </form>

          </div>

        </div>



        <div class="card">

          <div class="card-header bg-success text-white"><i class="fas fa-list-alt me-2"></i>Lista de Cupos Disponibles</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Profesor</th>

                    <th>Fechas</th> <!-- Cambiado a Fechas -->

                    <th>Horario</th>

                    <th>Cupo Disponible</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaCupos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Profesores -->

      <div class="tab-pane fade" id="profesores" role="tabpanel" aria-labelledby="profesores-tab">

        <div class="card mb-4">

          <div class="card-header bg-info text-white"><i class="fas fa-user-tie me-2"></i>Registro de Profesores</div>

          <div class="card-body">

            <form id="profesorForm">

              <input type="hidden" id="idProfesorEditando">

              <div class="form-group-grid">

                <div><label for="nombreProfesor" class="form-label">Nombre Completo</label><input type="text" class="form-control" id="nombreProfesor" required></div>

                <div><label for="correoProfesor" class="form-label">Correo Electrónico</label><input type="email" class="form-control" id="correoProfesor" required></div>

                <div><label for="telefonoProfesor" class="form-label">Teléfono</label><input type="tel" class="form-control" id="telefonoProfesor"></div>

              </div>

              <div class="d-grid mt-4">

                <button type="submit" class="btn btn-info" id="btnRegistrarProfesor"><i class="fas fa-user-plus me-2"></i>Registrar Profesor</button>

              </div>

            </form>

          </div>

        </div<!DOCTYPE html>

<html lang="es">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Sistema de Gestión de Alumnos y Cupos</title>

  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">

  <!-- CDN para SheetJS (librería para exportar a Excel) -->

  <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>

  <style>

    :root {

      --primary-color: #6f42c1; /* Morado */

      --secondary-color: #6c757d; /* Gris */

      --success-color: #28a745; /* Verde */

      --info-color: #17a2b8; /* Azul claro */

      --warning-color: #ffc107; /* Amarillo */

      --danger-color: #dc3545; /* Rojo */

      --light-bg: #f8f9fa;

      --dark-text: #343a40;

      --card-bg: #ffffff;

    }



    body {

      font-family: 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;

      background-color: var(--light-bg);

      color: var(--dark-text);

      line-height: 1.6;

    }



    .container {

      max-width: 1200px;

      margin-top: 30px;

      margin-bottom: 30px;

    }



    h1 {

      color: var(--primary-color);

      font-weight: 700;

      margin-bottom: 30px;

      text-align: center;

      position: relative;

    }

    h1::after {

      content: '';

      display: block;

      width: 80px;

      height: 4px;

      background-color: var(--primary-color);

      margin: 15px auto 0;

      border-radius: 2px;

    }



    .card {

      border-radius: 15px;

      box-shadow: 0 10px 25px rgba(0,0,0,0.1);

      border: none;

      background-color: var(--card-bg);

      margin-bottom: 25px;

    }



    .card-header {

      border-radius: 15px 15px 0 0 !important;

      font-size: 1.35rem;

      font-weight: 600;

      padding: 1.25rem 1.5rem;

      color: white;

      background-color: var(--primary-color); /* Default header color */

      border-bottom: none;

    }

    .card-header.bg-primary { background-color: var(--primary-color) !important; }

    .card-header.bg-success { background-color: var(--success-color) !important; }

    .card-header.bg-info { background-color: var(--info-color) !important; }

    .card-header.bg-secondary { background-color: var(--secondary-color) !important; }





    .nav-tabs {

      border-bottom: 2px solid var(--primary-color);

      margin-bottom: 25px;

    }

    .nav-tabs .nav-link {

      color: var(--dark-text);

      border: none;

      border-bottom: 3px solid transparent;

      padding: 12px 20px;

      font-weight: 500;

      transition: all 0.3s ease;

    }

    .nav-tabs .nav-link:hover {

      color: var(--primary-color);

      border-color: var(--primary-color);

    }

    .nav-tabs .nav-link.active {

      color: var(--primary-color);

      background-color: transparent;

      border-color: var(--primary-color);

      font-weight: 600;

    }



    .form-group-grid {

      display: grid;

      grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));

      gap: 1.5rem;

    }

    .form-group-full {

      grid-column: 1 / -1;

    }



    .form-control, .form-select {

      border-radius: 8px;

      border: 1px solid #ced4da;

      padding: 0.75rem 1rem;

      transition: all 0.3s ease;

    }

    .form-control:focus, .form-select:focus {

      border-color: var(--primary-color);

      box-shadow: 0 0 0 0.25rem rgba(111, 66, 193, 0.25); /* primary-color with alpha */

    }



    .btn {

      border-radius: 8px;

      padding: 0.75rem 1.5rem;

      font-weight: 600;

      transition: all 0.3s ease;

    }

    .btn-primary { background-color: var(--primary-color); border-color: var(--primary-color); }

    .btn-primary:hover { background-color: #5a36a3; border-color: #5a36a3; } /* Darker primary */

    .btn-info { background-color: var(--info-color); border-color: var(--info-color); }

    .btn-info:hover { background-color: #138496; border-color: #138496; } /* Darker info */

    .btn-warning { background-color: var(--warning-color); border-color: var(--warning-color); color: var(--dark-text); }

    .btn-warning:hover { background-color: #e0a800; border-color: #e0a800; }

    .btn-danger { background-color: var(--danger-color); border-color: var(--danger-color); }

    .btn-danger:hover { background-color: #bd2130; border-color: #bd2130; }

    .btn-secondary { background-color: var(--secondary-color); border-color: var(--secondary-color); }

    .btn-secondary:hover { background-color: #545b62; border-color: #545b62; }



    .table {

      margin-top: 15px;

      border-radius: 10px;

      overflow: hidden; /* Ensures rounded corners for table */

    }

    .table thead th {

      background-color: var(--primary-color);

      color: white;

      border-bottom: none;

      padding: 1rem;

    }

    .table tbody tr:nth-of-type(odd) {

      background-color: rgba(0,0,0,.03);

    }

    .table tbody tr:hover {

      background-color: rgba(111, 66, 193, 0.1); /* Light hover effect */

    }

    .table td, .table th {

      vertical-align: middle;

      border-top: 1px solid #dee2e6;

      padding: 0.85rem;

    }



    /* Autocomplete styles */

    .autocomplete-items {

      position: absolute;

      border: 1px solid #d4d4d4;

      border-bottom: none;

      border-top: none;

      z-index: 99;

      top: 100%;

      left: 0;

      right: 0;

      max-height: 200px;

      overflow-y: auto;

      background-color: var(--card-bg);

      border-radius: 0 0 8px 8px;

      box-shadow: 0 4px 10px rgba(0,0,0,0.1);

    }

    .autocomplete-items div {

      padding: 10px;

      cursor: pointer;

      background-color: var(--card-bg);

      border-bottom: 1px solid #e9ecef;

      transition: background-color 0.2s ease;

    }

    .autocomplete-items div:hover {

      background-color: #e9e9e9;

    }

    .autocomplete-active {

      background-color: var(--primary-color) !important;

      color: #ffffff;

    }

    .autocomplete-active strong {

      color: #ffffff;

    }



    /* Estilos para las fechas seleccionadas */

    .fecha-tag {

      display: inline-block;

      background-color: var(--primary-color);

      color: white;

      padding: 5px 10px;

      border-radius: 5px;

      margin: 5px;

      font-size: 0.9em;

    }

    .fecha-tag .btn-close-tag {

      background: none;

      border: none;

      color: white;

      font-size: 0.8em;

      margin-left: 5px;

      cursor: pointer;

    }



    /* Estilos para los toasts */

    .toast-container {

      position: fixed;

      top: 1rem;

      right: 1rem;

      z-index: 1080; /* Higher than modals */

    }

    .toast {

      border-radius: 0.5rem;

      box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);

      border: none;

    }

    .toast-header {

      border-bottom: none;

      font-weight: 600;

      color: white;

      padding: 0.75rem 1rem;

      border-radius: 0.5rem 0.5rem 0 0;

    }

    .toast-body {

      padding: 1rem;

      color: var(--dark-text);

    }

    .toast.bg-success .toast-header { background-color: var(--success-color); }

    .toast.bg-danger .toast-header { background-color: var(--danger-color); }

    .toast.bg-info .toast-header { background-color: var(--info-color); }

    .toast.bg-warning .toast-header { background-color: var(--warning-color); color: var(--dark-text); }

    .toast-header .btn-close {

      filter: invert(1) grayscale(100%) brightness(200%); /* Make close button white */

    }



    /* Ajuste para los select de Pago y Kit en el modal */

    #modalAsignarProyecto .form-group-grid > div {

      flex: 1; /* Distribute space evenly */

      min-width: unset; /* Override min-width for smaller selects */

    }

    #modalAsignarProyecto .form-select {

      padding: 0.5rem 0.75rem; /* Make selects smaller */

      font-size: 0.9rem;

    }

  </style>

</head>

<body>

  <div class="container">

    <h1><i class="fas fa-graduation-cap me-2"></i>Sistema de Gestión de Alumnos y Cupos</h1>



    <ul class="nav nav-tabs mb-4" id="myTab" role="tablist">

      <li class="nav-item" role="presentation">

        <button class="nav-link active" id="registro-tab" data-bs-toggle="tab" data-bs-target="#registro" type="button" role="tab" aria-controls="registro" aria-selected="true">

          <i class="fas fa-user-plus me-2"></i>Registro de Alumnos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="lista-tab" data-bs-toggle="tab" data-bs-target="#lista" type="button" role="tab" aria-controls="lista" aria-selected="false">

          <i class="fas fa-users me-2"></i>Alumnos Inscritos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="plantilla-tab" data-bs-toggle="tab" data-bs-target="#plantilla" type="button" role="tab" aria-controls="plantilla" aria-selected="false">

          <i class="fas fa-address-book me-2"></i>Alumnos Registrados

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="cupos-tab" data-bs-toggle="tab" data-bs-target="#cupos" type="button" role="tab" aria-controls="cupos" aria-selected="false">

          <i class="fas fa-calendar-alt me-2"></i>Cupos

        </button>

      </li>

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="profesores-tab" data-bs-toggle="tab" data-bs-target="#profesores" type="button" role="tab" aria-controls="profesores" aria-selected="false">

          <i class="fas fa-chalkboard-teacher me-2"></i>Profesores

        </button>

      </li>

      <!-- Nueva pestaña para Proyectos -->

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="proyectos-tab" data-bs-toggle="tab" data-bs-target="#proyectos" type="button" role="tab" aria-controls="proyectos" aria-selected="false">

          <i class="fas fa-project-diagram me-2"></i>Proyectos

        </button>

      </li>

      <!-- Nueva pestaña para Reportes -->

      <li class="nav-item" role="presentation">

        <button class="nav-link" id="reportes-tab" data-bs-toggle="tab" data-bs-target="#reportes" type="button" role="tab" aria-controls="reportes" aria-selected="false">

          <i class="fas fa-chart-bar me-2"></i>Reportes

        </button>

      </li>

    </ul>



    <div class="tab-content" id="myTabContent">

      <!-- Registro de Alumnos -->

      <div class="tab-pane fade show active" id="registro" role="tabpanel" aria-labelledby="registro-tab">

        <div class="card">

          <div class="card-header bg-primary text-white"><i class="fas fa-user-plus me-2"></i>Registro de Alumnos</div>

          <div class="card-body">

            <form id="alumnoForm">

              <input type="hidden" id="idAlumnoEditando">

              <input type="hidden" id="idAlumnoPlantilla">



              <div class="mb-4">

                <label for="buscarAlumno" class="form-label fw-bold">Buscar Alumno Existente para Reinscripción</label>

                <div class="position-relative">

                  <input type="text" class="form-control" id="buscarAlumno" placeholder="Buscar por nombre o correo electrónico">

                  <div id="autocompleteList" class="autocomplete-items"></div>

                </div>

                <small class="form-text text-muted">Empieza a escribir para buscar alumnos registrados previamente.</small>

              </div>



              <div class="form-group-grid">

                <div><label for="nombre" class="form-label">Nombre Completo</label><input type="text" class="form-control" id="nombre" required></div>

                <div><label for="nivel" class="form-label">Nivel</label>

                  <select class="form-select" id="nivel" required>

                    <option value="">Seleccione</option>

                    <option value="De cero">De cero</option>

                    <option value="Basico">Básico</option>

                    <option value="Intermedio">Intermedio</option>

                    <option value="Avanzado">Avanzado</option>

                  </select>

                </div>

                <div><label for="telefono" class="form-label">Teléfono</label><input type="tel" class="form-control" id="telefono" required></div>

                <div><label for="correo" class="form-label">Correo Electrónico</label><input type="email" class="form-control" id="correo" required></div>

                <div><label for="estatus" class="form-label">Estatus</label>

                  <select class="form-select" id="estatus" required>

                    <option value="">Seleccione</option>

                    <option value="Recurrente">Recurrente</option>

                    <option value="Nuevo">Nuevo</option>

                    <option value="Ya no asiste">Ya no asiste</option>

                  </select>

                </div>

                <!-- El select de horario se ha movido al modal de "Alumnos Inscritos" -->

              </div>

              <div class="d-grid gap-2 d-md-flex justify-content-md-end mt-4">

                <button type="submit" class="btn btn-primary" id="btnRegistrarAlumno"><i class="fas fa-save me-2"></i>Registrar Alumno</button>

                <button type="button" class="btn btn-secondary" id="btnLimpiarAlumnoForm"><i class="fas fa-eraser me-2"></i>Limpiar Formulario</button>

              </div>

            </form>

          </div>

        </div>

      </div>



      <!-- Lista de Alumnos Inscritos (Activos en un cupo) -->

      <div class="tab-pane fade" id="lista" role="tabpanel" aria-labelledby="lista-tab">

        <div class="card">

          <div class="card-header bg-success text-white"><i class="fas fa-users me-2"></i>Alumnos Inscritos (Activos en un Cupo)</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Nombre</th>

                    <th>Nivel</th>

                    <th>Horario</th>

                    <th>Proyecto Asignado</th>

                    <th>Pago</th> <!-- Nueva columna -->

                    <th>Kit</th> <!-- Nueva columna -->

                    <th>Observaciones</th>

                    <th>Estatus</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaAlumnos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Lista de Alumnos Registrados (Plantilla) -->

      <div class="tab-pane fade" id="plantilla" role="tabpanel" aria-labelledby="plantilla-tab">

        <div class="card">

          <div class="card-header bg-secondary text-white"><i class="fas fa-address-book me-2"></i>Alumnos Registrados (Plantilla)</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Nombre</th>

                    <th>Correo</th>

                    <th>Teléfono</th>

                    <th>Nivel Preferido</th>

                    <th>Observaciones</th>

                    <th>Estado</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaPlantillaAlumnos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Cupos -->

      <div class="tab-pane fade" id="cupos" role="tabpanel" aria-labelledby="cupos-tab">

        <div class="card mb-4">

          <div class="card-header bg-primary text-white"><i class="fas fa-calendar-plus me-2"></i>Registro de Nuevos Cupos</div>

          <div class="card-body">

            <form id="cupoForm">

              <div class="form-group-grid">

                <div><label for="profesorSelect" class="form-label">Profesor</label>

                  <select class="form-select" id="profesorSelect" required>

                    <option value="">Seleccione un profesor</option>

                  </select>

                </div>

                <div>

                  <label for="fechaCupoIndividual" class="form-label">Añadir Fechas</label>

                  <div class="input-group">

                    <input type="date" class="form-control" id="fechaCupoIndividual">

                    <button class="btn btn-outline-secondary" type="button" id="btnAddFechaCupo"><i class="fas fa-plus"></i> Añadir</button>

                  </div>

                  <div id="fechasSeleccionadasContainer" class="mt-2">

                    <!-- Aquí se mostrarán las fechas seleccionadas -->

                  </div>

                  <input type="hidden" id="fechasCupoHidden" required> <!-- Campo oculto para el submit -->

                </div>

                <div><label for="horaInicio" class="form-label">Hora Inicio</label><input type="time" class="form-control" id="horaInicio" required></div>

                <div><label for="horaFin" class="form-label">Hora Fin</label><input type="time" class="form-control" id="horaFin" required></div>

                <div><label for="cupoMax" class="form-label">Cupo (Máx. Personas)</label><input type="number" class="form-control" id="cupoMax" required min="1"></div>

              </div>

              <div class="d-grid mt-4">

                <button type="submit" class="btn btn-primary"><i class="fas fa-plus-circle me-2"></i>Registrar Cupo</button>

              </div>

            </form>

          </div>

        </div>



        <div class="card">

          <div class="card-header bg-success text-white"><i class="fas fa-list-alt me-2"></i>Lista de Cupos Disponibles</div>

          <div class="card-body">

            <div class="table-responsive">

              <table class="table table-striped table-hover">

                <thead>

                  <tr>

                    <th>Profesor</th>

                    <th>Fechas</th> <!-- Cambiado a Fechas -->

                    <th>Horario</th>

                    <th>Cupo Disponible</th>

                    <th>Acciones</th>

                  </tr>

                </thead>

                <tbody id="listaCupos"></tbody>

              </table>

            </div>

          </div>

        </div>

      </div>



      <!-- Profesores -->

      <div class="tab-pane fade" id="profesores" role="tabpanel" aria-labelledby="profesores-tab">

        <div class="card mb-4">

          <div class="card-header bg-info text-white"><i class="fas fa-user-tie me-2"></i>Registro de Profesores</div>

          <div class="card-body">

            <form id="profesorForm">

              <input type="hidden" id="idProfesorEditando">

              <div class="form-group-grid">

                <div><label for="nombreProfesor" class="form-label">Nombre Completo</label><input type="text" class="form-control" id="nombreProfesor" required></div>

                <div><label for="correoProfesor" class="form-label">Correo Electrónico</label><input type="email" class="form-control" id="correoProfesor" required></div>

                <div><label for="telefonoProfesor" class="form-label">Teléfono</label><input type="tel" class="form-control" id="telefonoProfesor"></div>

              </div>

              <div class="d-grid mt-4">

                <button type="submit" class="btn btn-info" id="btnRegistrarProfesor"><i class="fas fa-user-plus me-2"></i>Registrar Profesor</button>

              </div>

            </form>

          </div>

        </div
