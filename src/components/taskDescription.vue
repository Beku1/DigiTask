<template>
  <div class="task-description">
    <span class="icon-lg task-description-symbol icon-description"></span>
    <p class="task-description-title">Description</p>
    <div
      class="task-description-content"
      v-click-outside="saveDesc"
      v-if="descEdit"
    >
      <form @submit="saveDesc">
        <textarea
          @focus="$event.target.select()"
          class="textarea-another-list"
          ref="desc"
          v-click-outside="saveDesc"
          oninput='this.style.height = "";this.style.height = this.scrollHeight + "px"'
          onfocus='this.style.height = "";this.style.height = this.scrollHeight + "px"'
          v-model="updatedTask.description"
          maxlength="512"
          placeholder="Add a more detailed description..."
        />
        <div class="task-description-buttons">
          <button type="submit" class="task-description-save">Save</button>
          <button @click="clearDesc" class="task-description-close">
            <span class="icon-lg close-icon"> </span>
          </button>
        </div>
      </form>
    </div>

    <div v-else>
      <p class="task-description-placeholder" @click="editDesc">
        {{ emptyDesc }}
      </p>
    </div>
  </div>
</template>
<script>
import vClickOutside from "v-click-outside";
export default {
  props: ["task", "descEdit"],
  name: "taskDescription",
  data() {
    return {
      updatedTask: "",
    };
  },
  created() {
    this.updatedTask = JSON.parse(JSON.stringify(this.task));
  },
  methods: {
    saveDesc() {
      let task = JSON.parse(JSON.stringify(this.task));
      task.description = this.updatedTask.description;
      this.$emit("saveEdit", task);
    },
    clearDesc() {
      this.updatedTask.description = JSON.parse(
        JSON.stringify(this.task.description)
      );
      this.$emit("closeDescEdit");
    },
    editDesc() {
      this.$emit("editDesc");
      this.$nextTick(() => {
        this.$refs.desc.focus();
      });
    },
  },
  computed: {
    emptyDesc() {
      return !this.updatedTask.description.match(/^\s*$/)
        ? this.updatedTask.description
        : "Add a more detailed description...";
    },
  },
  directives: {
    clickOutside: vClickOutside.directive,
  },
};
</script>