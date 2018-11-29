<template>
  <div id="app" class="container">
    <div class="jumbotron text-center">
      <h1 class="display-3">Administrador de tareas.</h1>
      <p class="lead">Añade y calcula el tiempo de tus actividades.</p>
    </div>
      <div class="col-md-12 p-0">
    <div class="row">
      <div class="col-lg-12" v-show="totalTime > 0">
        <div class="bs-component">
          <div class="alert alert-dismissible alert-warning">
            Total de horas programadas:<strong> {{this.totalTime}}</strong>.
          </div>
        </div>
        <div class="progress mt-3 mb-3">
          <div class="progress-bar" role="progressbar" aria-valuenow="" aria-valuemin="0" aria-valuemax="100" id="progressbar">{{progress}}%</div>
        </div>
      </div>
        <div class="col-md-4">
          <div class="card border-success mb-3">
            <div class="card-header text-white bg-success">
              <h5 class="card-title">Añadir de tarea</h5>
            </div>
            <div class="card-body">
              <form action="" @submit.prevent="addTask">
                <div class="form-group">
                  <label for="title">Titulo de la tarea</label>
                  <input type="text" class="form-control" id="title" placeholder="Ingresa el titulo de la tarea" v-model="newTask.title">
                </div>
                <div class="form-group">
                  <label for="time">Tiempo estimado</label>
                  <input type="number" class="form-control" id="time" aria-describedby="timeHelp"  v-model="newTask.time">
                  <small id="timeHelp" class="form-text text-muted">Tiene que ser un valor mayor que 0.</small>
                </div>
                <div class="text-center">
                  <button type="button" class="btn btn-danger" @click="cancel">Cancelar</button>
                  <button type="submit" class="btn btn-success" :disabled="validateTask">Guardar</button>
                </div>
              </form>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card border-success">
            <div class="card-header text-white bg-success">
              <h5 class="card-title">Lista de tareas</h5>
            </div>
            <div class="card-body p-0">
              <ul class="list-group">
                <li v-for="(task, index) in tasks" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
                  <span><span class="badge badge-danger badge-pill delete" @click="removeTask(index,task.time)">&times;</span>  {{task.title}} </span>
                  <span><span class="badge badge-primary badge-pill">{{task.time}}</span> <span class="badge badge-success badge-pill" @click="completar(index)">V</span></span>
                </li>
                <li  v-if="tasks.length == 0" class="list-group-item d-flex justify-content-between align-items-center">
                   <span>Aun no tienes tareas disponibles.</span>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card border-success">
            <div class="card-header text-white bg-success">
              <h5 class="card-title">Tareas completadas</h5>
            </div>
            <div class="card-body p-0">
              <ul class="list-group">
                <li v-for="(task, index) in tasksCompleted" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
                  <span>  {{task.title}} </span>
                  <span class="badge badge-primary badge-pill">{{task.time}}  </span>
                </li>
                <li  v-if="tasksCompleted.length == 0" class="list-group-item d-flex justify-content-between align-items-center">
                   <span>No has completado tareas.</span>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      newTask:{
        title:'',
        time:''
      },
      tasks:[],
      tasksCompleted:[],
      totalTime:0,
      progress:0
    }
  },
  created:function(){
    this.tasks = JSON.parse(localStorage.getItem('tasks')) || [];
    this.tasksCompleted = JSON.parse(localStorage.getItem('tasksCompleted')) || [];
    if (this.tasks.length > 0) {
      for(var task of this.tasks){
          this.totalTime += parseInt(task.time);
      }
    }
  },
  mounted: function(){
    this.calculateProgress();
  },
  computed:{
    validateTask(){
      return !(this.newTask.title != '' && this.newTask.time != '' && this.newTask.time > 0);
    }
  },
  methods:{
    addTask(){
      this.tasks.push({title:this.newTask.title,time:this.newTask.time});
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
      this.totalTime += parseInt(this.newTask.time);
      this.newTask.title = '';
      this.newTask.time = '';
      this.calculateProgress();
    },
    cancel(){
      this.newTask.title = '';
      this.newTask.time = '';
    },
    removeTask(index,time){
      this.tasks.splice(index, 1);
      this.totalTime -= parseInt(time);
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
      this.calculateProgress();
    },
    completar(index){
      var task = this.tasks[index];
      this.tasks.splice(index, 1);
      this.tasksCompleted.push(task);
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
      localStorage.setItem('tasksCompleted', JSON.stringify(this.tasksCompleted));
      this.calculateProgress();
    },
    calculateProgress(){
      var totalTask = this.tasks.length + this.tasksCompleted.length;
      var progress =   parseInt((this.tasksCompleted.length / totalTask) * 100);
      this.progress = progress;
      document.getElementById('progressbar').style.width = `${progress}%`;
    }
  }
}
</script>

<style>
@import './css/bootstrap.min.css';
.delete{
  cursor: pointer;
  font-weight: bold;
}
</style>
