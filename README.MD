# Explicación del Código de la Aplicación Lista de Tareas

#### Esta aplicación de lista de tareas permite a los usuarios agregar, eliminar y visualizar tareas con diferentes niveles de prioridad. Se ha desarrollado utilizando **Vue.js** y **Ionic**, lo que permite crear una interfaz de usuario interactiva y receptiva.

## Estructura del Código

### 1. Template
La parte del template está diseñada con Ionic y Vue. Incluye los siguientes elementos:

**Encabezado**: Muestra el título de la aplicación.
  
```html
<ion-header>
  <ion-toolbar>
    <ion-title>Lista de Tareas</ion-title>
  </ion-toolbar>
</ion-header>
```
- `<ion-header>`: Contenedor principal para la cabecera de la página.
- `<ion-toolbar>`: Componente que proporciona una barra de herramientas.
- `<ion-title>`: Muestra el título "Lista de Tareas".

**b. Entrada para Nueva Tarea**: Permite al usuario ingresar la descripción de una nueva tarea.

```html
<ion-item>
  <ion-label position="floating">Nueva Tarea</ion-label>
  <ion-input placeholder="Descripción de la tarea" v-model="newTask.description"></ion-input>
</ion-item>
```
- `<ion-item>`: Componente que agrupa elementos relacionados.
- `<ion-label>`: Etiqueta para el campo de entrada; `position="floating"` hace que flote cuando se ingresa texto.
- `<ion-input>`: Campo donde el usuario puede escribir la descripción de la tarea.
  * `v-model="newTask.description"`: Se utiliza para vincular el valor del input con la propiedad `description` del objeto `newTask`.

**c. Selector de Prioridad**: Un menú desplegable para seleccionar la prioridad de la tarea (alta, media, baja).

```html
<ion-item>
  <ion-label>Prioridad</ion-label>
  <ion-select v-model="newTask.priority" placeholder="Selecciona">
    <ion-select-option value="alta">Alta</ion-select-option>
    <ion-select-option value="media">Media</ion-select-option>
    <ion-select-option value="baja">Baja</ion-select-option>
  </ion-select>
</ion-item>
```
- `<ion-select>`: Componente que presenta un menú desplegable.
  * `v-model="newTask.priority"`: Se vincula a la propiedad priority del objeto newTask, permitiendo almacenar la prioridad seleccionada.

- `<ion-select-option>`: Cada opción dentro del menú desplegable que permite seleccionar la prioridad (alta, media, baja). 

**d. Checkbox para Mostrar Completadas**: Permite al usuario decidir si desea ver las tareas completadas o no.

```html
<ion-item>
  <ion-label>Mostrar Completadas</ion-label>
  <ion-checkbox v-model="showCompleted"></ion-checkbox>
</ion-item>
```
`<ion-checkbox>`: Permite a los usuarios seleccionar o deseleccionar para mostrar las tareas completadas.

* `v-model="showCompleted"`: Vincula el estado del checkbox a la variable reactiva `showCompleted`.

**e.    Botón para Agregar Tarea:** Botón que llama a la función `addTask` para agregar una nueva tarea.

```html
<ion-button expand="block" @click="addTask">Agregar Tarea</ion-button>
```
- `<ion-button>`: Componente que representa un botón.

  * `@click="addTask"`: Escucha el evento de clic y ejecuta la función `addTask` cuando se hace clic en el botón.

**f. Lista de Tareas:** Muestra las tareas filtradas según su estado de completado.
```html
<ion-list>
  <ion-item v-for="(task, index) in filteredTasks" :key="index">
    <ion-label>
      <h2>{{ task.description }}</h2>
      <p>Prioridad: {{ task.priority }}</p>
    </ion-label>
    <ion-checkbox slot="start" v-model="task.completed"></ion-checkbox>
    <ion-button slot="end" color="danger" @click="deleteTask(index)">Eliminar</ion-button>
  </ion-item>
</ion-list>
```
- `<ion-list>`: Componente que agrupa elementos de lista.

- `v-for="(task, index) in filteredTasks"`: Directiva que itera sobre el array `filteredTasks`, donde cada `task` representa una tarea y `index` es su índice.

- `:key="index"`: Propiedad única para cada elemento en la lista, lo que ayuda a Vue a identificar qué elementos han cambiado, se han agregado o eliminado.

- `<h2>{{ task.description }}</h2>`: Muestra la descripción de la tarea.

- `<p>Prioridad: {{ task.priority }}</p>`: Muestra la prioridad de la tarea.

- `<ion-checkbox slot="start" v-model="task.completed"></ion-checkbox>`: Checkbox que permite marcar la tarea como completada. Se vincula a la propiedad `completed` de la tarea.

- `<ion-button slot="end" color="danger" @click="deleteTask(index)">Eliminar</ion-button>`: Botón para eliminar la tarea. Al hacer clic, ejecuta `deleteTask` pasando el índice de la tarea.

**g. Mensajes de Confirmación:** Toasts que informan al usuario cuando una tarea ha sido agregada o eliminada.
```html
<ion-toast :isOpen="showToast" message="Tarea agregada" duration="1500" @didDismiss="showToast = false" />
<ion-toast :isOpen="showDeleteToast" message="Tarea eliminada" duration="1500" @didDismiss="showDeleteToast = false" />
```
* `<ion-toast>`: Componente que muestra un mensaje emergente temporal.
  * `:isOpen="showToast"`: Controla si el toast está visible basado en el estado de `showToast.`

  * `message="Tarea agregada"`: Mensaje que se muestra en el toast.

  * `duration="1500"`: Duración en milisegundos que el toast permanecerá visible.

  * `@didDismiss="showToast = false"`: Evento que se dispara cuando el toast se cierra, configurando `showToast` a `false`. 

## 2. Script
La lógica de la aplicación se maneja en la sección del script. Aquí se definen las variables reactivas y los métodos:

* a. Importaciones:
Se importan componentes de Ionic y funciones de Vue necesarias.

```javascript 
import { ref, computed } from 'vue';
import {
  IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonItem, IonLabel, IonInput,
  IonButton, IonList, IonCheckbox, IonSelect, IonSelectOption, IonToast
} from '@ionic/vue';
```
* `ref`: Función de Vue que crea una referencia reactiva.
* `computed`: Función que permite crear propiedades computadas.
* Importa componentes de Ionic que se utilizarán en el template.

### b.Definición de Componente
Se define el componente y sus propiedades

```javascript 
 export default {
  components: {
    IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonItem, IonLabel, IonInput,
    IonButton, IonList, IonCheckbox, IonSelect, IonSelectOption, IonToast
  },
  setup() {
    // Lógica aquí...
  }
};
```
* `components`: Registra los componentes de Ionic utilizados en el template.

 ### c. Variables Reactivas
 Se crean variables reactivas que mantienen el estado de la aplicación. 

```javascript
const tasks = ref([
  { description: 'Comprar leche', priority: 'baja', completed: false },
  { description: 'Lavar la ropa', priority: 'media', completed: true },
  { description: 'Ir al gimnasio', priority: 'alta', completed: false }
]);
const newTask = ref({ description: '', priority: 'baja', completed: false });
const showToast = ref(false);
const showDeleteToast = ref(false);
const showCompleted = ref(false);
```
* `tasks`: Array que almacena las tareas iniciales. Cada tarea es un objeto con `description`, priority y `completed`.

* `newTask`: Objeto que contiene la información de la nueva tarea a agregar.

* `showToast`: Booleano que controla la visibilidad del toast que confirma la adición de una tarea.

* `showDeleteToast`: Booleano que controla la visibilidad del toast que confirma la eliminación de una tarea.

* `showCompleted`: Booleano que indica si las tareas completadas deben ser mostradas.

* Funciones:
  * **addTask:** Agrega una nueva tarea a la lista.
  * **deleteTask:** Elimina una tarea de la lista según su índice.

```javascript
function addTask() {
  // lógica para agregar tarea
}

function deleteTask(index) {
  // lógica para eliminar tarea
}
```
## 3. Estilo
El estilo es scoped para evitar que afecte a otros componentes. Se han agregado márgenes para mejorar la presentación.

```css
<style scoped>
ion-item {
  margin-bottom: 10px;
}
ion-checkbox {
  margin-right: 15px;
}
</style>
```
### d. Propiedad Computada
Se define una propiedad computada para filtrar las tareas.
```javascript 
const filteredTasks = computed(() => {
  return tasks.value.filter(task => showCompleted || !task.completed);
});
```
`filteredTasks`: Devuelve un nuevo array de tareas filtradas según el estado de `showCompleted`.
## e. Funciones
```javascript
function addTask() {
  if (newTask.value.description.trim()) {
    tasks.value.push({ ...newTask.value });
    newTask.value = { description: '', priority: 'baja', completed: false };
    showToast.value = true;
  }
}

function deleteTask(index) {
  tasks.value.splice(index, 1);
  showDeleteToast.value = true;
}
```
* `addTask`: Agrega una tarea si la descripción no está vacía. Resetea newTask y muestra el toast.

* `deleteTask`: Elimina una tarea usando su índice y muestra el toast correspondiente.

## 3. Estilo
```css
<style scoped>
ion-item {
  margin-bottom: 10px;
}
ion-checkbox {
  margin-right: 15px;
}
</style>
```
* `scoped`: Asegura que el estilo solo afecte a este componente.

* Se añaden márgenes a los elementos para mejorar la presentación.

## **Conclusión**
La aplicación de lista de tareas permite a los usuarios gestionar sus tareas de manera eficiente. Las funciones están implementadas de forma clara, permitiendo agregar y eliminar tareas, así como seleccionar prioridades y filtrar tareas completadas. Se puede mejorar añadiendo persistencia de datos o características adicionales como edición de tareas.

## 
![Modelo relacional del ejercicio](/imagenes/image.png)
![Modelo relacional del ejercicio](/imagenes/image1.png)
![Modelo relacional del ejercicio](/imagenes/image2.png)
![Modelo relacional del ejercicio](/imagenes/image3.png)
