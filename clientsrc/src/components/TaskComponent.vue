<template>
  <div
    @dragstart="moveTaskDrag()"
    class="task-component col-11 border-top border-bottom shadow bg-info"
  >
    <div class="row">
      <div v-if="!taskForm" class="dropdown col-12 mt-1 px-0">
        <a
          class="btn dropdown dropdown-toggle text-wrap text-dark"
          href="#"
          role="button"
          id="dropdownMenuLink"
          data-toggle="dropdown"
          aria-haspopup="true"
          aria-expanded="false"
          >{{ task.title }}</a
        >

        <div class="dropdown-menu" aria-labelledby="dropdownMenuLink">
          <a class="dropdown-item " @click="commentForm = !commentForm" href="#"
            >Add Comment</a
          >
          <a class="dropdown-item" @click="taskForm = !taskForm" href="#"
            >Edit</a
          >
          <a class="dropdown-item " @click="showDeleteTaskAlert" href="#"
            >Delete Task</a
          >
          <a
            class="dropdown-item "
            @click="moveTaskForm = !moveTaskForm"
            href="#"
            >MoveTask</a
          >
        </div>
      </div>
    </div>
    <form
      class="form-inline justify-content-center col-12 my-2"
      v-if="taskForm"
      @submit.prevent="editTask"
    >
      <input
        class="form-control col-lg-8 mx-2"
        type="text"
        placeholder="title"
        v-model="task.title"
        required
      />
      <button class="btn btn-warning btn-outline-dark" type="submit">
        +
      </button>
    </form>
    <form
      class="form-inline justify-content-center col-12 my-2"
      v-if="commentForm"
      @submit.prevent="addComment"
    >
      <input
        class="form-control col-lg-8 mx-2"
        type="text"
        placeholder="title"
        v-model="newComment.title"
        required
      />
      <button class="btn btn-warning btn-outline-dark" type="submit">
        +
      </button>
    </form>
    <div class="row justify-content-center" v-if="moveTaskForm">
      <div
        class="border col-8 bg-orange text-dark rounded shadow action my-1"
        v-for="list in lists"
        :key="list.id"
        @click="moveTask({ id: list.id })"
      >
        {{ list.title }}
      </div>
    </div>
    <div class="row">
      <div class="col-12">
        <comment
          v-for="comment in comments"
          :key="comment.id"
          :comment="comment"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Comment from "@/components/CommentComponent";
export default {
  name: "task-component",
  data() {
    return {
      commentForm: false,
      newComment: {
        taskId: this.task.id,
        listId: this.task.listId,
      },
      taskForm: false,
      moveTaskForm: false,
    };
  },
  computed: {
    comments() {
      return this.task.comments;
    },
    lists() {
      return this.$store.state.lists;
    },
  },
  methods: {
    addComment() {
      this.$store.dispatch("addComment", { ...this.newComment });
      this.newComment = {
        taskId: this.task.id,
        listId: this.task.listId,
      };
      this.commentForm = false;
    },
    deleteTask() {
      this.$store.dispatch("deleteTask", this.task);
    },
    editTask() {
      this.$store.dispatch("editTask", this.task);
      this.taskForm = false;
    },
    moveTask(data) {
      this.$store.dispatch("moveTask", {
        listId: data.id,
        id: this.task.id,
        oldListId: this.task.listId,
      });
    },
    showDeleteTaskAlert() {
      swal({
        title: "Are you sure?",
        text: "Once deleted, you will not be able to recover this task!",
        icon: "warning",
        buttons: true,
        dangerMode: true,
      }).then((willDelete) => {
        if (willDelete) {
          // swal("Task deleted!", {
          //   icon: "success",
          // });
          this.deleteTask();
        }
      });
    },
    moveTaskDrag() {
      console.log("drag starting");

      event.dataTransfer.setData("data", JSON.stringify(this.task));
      event.dataTransfer.setData("list", this.listId);
    },
  },
  components: {
    Comment,
  },
  props: ["task", "listId"],
};
</script>

<style scoped>
.action {
  cursor: pointer;
}
.bg-teal {
  background-color: #00bc8c;
}
.bg-orange {
  background-color: #b8c0a5;
}
</style>
