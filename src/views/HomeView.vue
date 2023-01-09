<template>
  <div class="container">
    <!--gegevenseigenschappen van de kolommen:een div wordt voor elke kolom weergeven. iedere kolom-div heeft een klasse 'column'en een binding op de index van de kolom in de array -->
    <div v-for="(column, index) in columns" :key="index" class="column">
      <div class="column-header">
        {{ column.name }}
      </div>
      <!-- Dit element heeft gebeurtenislisteners voor de dragover- en drop-gebeurtenissen, die worden gebruikt om de drag-and-drop-functionaliteit voor de taken te implementeren. Wanneer een taak op dit element wordt neergezet, wordt de methode onDrop aangeroepen met de index van de kolom en de gebeurtenis drop als argumenten.  ($event = gebeurtenis) -->
      <div
        ref="dragArea"
        class="drag-area"
        @dragover.prevent
        @drop="onDrop(index, $event)"
      >
        <!-- sleepgebied: die de taken in de kolom herhaalt en een div-element voor elke taak weergeeft. class taak staat gelijk aan de verbinding met de index van de taak in de takenreeks van de kolom.
         De task div heeft ook een dragstart-gebeurtenislistener die de onDragStart-methode aanroept met het taakobject en de dragstart-gebeurtenis als argumenten. Dit wordt gebruikt om de gegevens in te stellen die worden overgedragen wanneer de taak wordt gesleept.-->
        <div
          v-for="(task, taskIndex) in column.tasks"
          :key="taskIndex"
          class="task"
          @dragstart="onDragStart(task, $event)"
          draggable
        >
          <!-- De taak-div heeft ook het kenmerk versleepbaar, waardoor het geschikt is om te worden gesleept. -->

          <!-- invoerelement om tekst bij te voegen. De button plaatst een verwijder button bij. -->
          <input v-model="task.name" type="text" />
          <button @click="deleteTask(task, index)">Verwijderen</button>
        </div>
        <!-- hier kun je nieuwe taken bijvoegen aan de hand van de knop toevoegen -->
        <div>
          <input
            v-model="newTaskName"
            type="text"
            placeholder="Wat wens je te doen"
          />
          <button @click="addTask(index)">Toevoegen</button>
        </div>
        Versleep je kolom naar hier
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      columns: [
        {
          name: "Wat te doen",
          tasks: [{ name: "" }, { name: "" }, { name: "" }],
        },
        {
          name: "In uitvoering",
          tasks: [{ name: "" }, { name: "" }],
        },
        {
          name: "Compleet",
          tasks: [{ name: "" }, { name: "" }, { name: "" }],
        },
      ],
      newTaskName: "",
    };
  },
  methods: {
    onDrop(columnIndex, event) {
      event.preventDefault();
      const task = JSON.parse(event.dataTransfer.getData("text"));
      this.columns[columnIndex].tasks.push(task);
    },
    onDragStart(task, event) {
      event.dataTransfer.setData("text", JSON.stringify(task));
    },
    deleteTask(task, columnIndex) {
      const index = this.columns[columnIndex].tasks.indexOf(task);
      if (index > -1) {
        this.columns[columnIndex].tasks.splice(index, 1);
      }
    },
    addTask(columnIndex) {
      this.columns[columnIndex].tasks.push({ name: this.newTaskName });
      this.newTaskName = "";
    },
  },
};
</script>

<style>
.container {
  display: flex;
}

.column {
  flex: 1;
  border: 1px solid #ccc;
  margin: 0 10px;
}

.column-header {
  background-color: #f2f2f2;
  padding: 10px;
  font-weight: bold;
}

.drag-area {
  min-height: 100px;
  border: 2px dashed #ccc;
  text-align: center;
  line-height: 100px;
  font-size: 20px;
  color: #ccc;
}

.task {
  background-color: #f8f8f8;
  padding: 10px;
  margin: 5px 0;
  cursor: move;
}
</style>
