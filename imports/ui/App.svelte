<script>
  import { TasksCollection } from '../api/TasksCollection';
  import Task from './Task.svelte';
  import TaskForm from './TaskForm.svelte';

  // Adding button to hide tasks
  let hideCompleted = false;

  const hideCompletedFilter = { isChecked: { $ne: true } };

  let incompleteCount;
  let pendingTasksTitle = '';
  let tasks = [];

  // filter tasks by if completed
  $m: {
    tasks = TasksCollection.find(hideCompleted ? hideCompletedFilter : {}, {
      sort: { createdAt: -1 },
    }).fetch();

    incompleteCount = TasksCollection.find(hideCompletedFilter).count();

    // keep track of number of pending tasks
    pendingTasksTitle = `${incompleteCount ? ` (${incompleteCount})` : ''}`;
  }

  // change status of button
  const setHideCompleted = (value) => {
    hideCompleted = value;
  };
</script>

<div class="app">
  <header>
    <div class="app-bar">
      <div class="app-header">
        <!--display number of pending tasks-->
        <h1>📝️ To Do List {pendingTasksTitle}</h1>
      </div>
    </div>
  </header>

  <div class="main">
    <!--import form for inputting new task-->
    <TaskForm />
    <!--button that toggles between hide completed and show all-->
    <div class="filter">
      <button on:click={() => setHideCompleted(!hideCompleted)}>
        {hideCompleted ? 'Show All' : 'Hide Completed'}
      </button>
    </div>
    <!--show tasks in a list-->
    <ul class="tasks">
      {#each tasks as task (task._id)}
        <Task task={task} />
      {/each}
    </ul>
  </div>
</div>