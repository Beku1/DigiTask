<template>
  <div class="task-addons-container" v-if="getTask">
    <div class="task-addons-content-container">
      <div class="task-addons-members" v-if="getTask.members.length">
        <p class="subtitles">Members</p>
        <div class="task-addons-members-info">
          <div v-for="member in getTask.members" :key="member._id">
            <span class="user-tag-name in-header" :title="member.fullname">
              <img
                class="image-settings"
                :src="member.imgUrl"
                :title="member.fullname"
              />
            </span>
          </div>
        </div>
      </div>
    </div>
    <div class="task-addons-content-container">
      <div class="task-addons-labels" v-if="getLabel">
        <p class="subtitles">Labels</p>
        <div class="task-addons-members-info">
          <div
            class="task-addons-labels-cards"
            v-for="label in getLabel"
            :key="label.id"
          >
            <div :style="'background-color:' + label.color" :title="label.title">
              <span v-if="label.title">{{ label.title }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="task-addons-content-container">
      <div class="task-addons-dates" v-if="dueDate || startDate">
        <p class="subtitles">Dates</p>
        <p></p>
        <div class="task-addons-dates-cards">
          <input type="checkbox" v-model="isDone" @change="saveIsDone" />
          <div class="dates-preview" title="Date">
            <span v-if="startDate">{{ startDate }} </span
            ><span v-if="dueDate"> {{ dueDate }}</span>
            <span v-if="getTask.dates.isDone" class="task-due-completed">
              complete</span
            >
          </div>
        </div>
      </div>
    </div>
            <task-description
              :task="getTask"
              @saveEdit="saveEdit"
              @editDesc="editDesc"
              :descEdit="descEdit"
              @closeDescEdit="closeDescEdit"
              title="Description"
            />

    <div class="task-addons-attachment-container" v-if="getTaskAttachments.length">
      <div >
        <div class="task-addons-att-title-container"> 
          <span class="icon-lg attachments att-symbol-settings"></span>
          <p class="task-addons-att-title">Attachments</p>
        </div>
        <div
          class="att-content"
          v-for="attachment in getTaskAttachments"
          :key="attachment.id"
        >
          <img :src="attachment.imgUrl" :title="attachment.txt"/>
          <div class="att-info">
            <div class="att-content-info">
              <p class="att-title">{{ attachment.txt }}</p>
              <p class="att-date">{{ attDate(attachment.createdAt) }}</p>
            </div>
            <div class="make-cover-btn-container">
              <span class="icon-sm cover-icon"></span>
              <button class="make-cover-btn" @click="setCover(attachment.imgUrl)" title="Set/Remove cover">{{isCoverImage(attachment.imgUrl)}}</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    
  </div>
</template>
<script>
import taskDescription from "../components/taskDescription.vue";
export default {
  name: "taskAddons",
  props: ["getTask", "getBoard","descEdit"],
  data() {
    return {
      isDone: false,
    };
  },
  created() {
    this.isDone = this.getTask.dates.isDone;
  },
  methods: {
    saveIsDone() {
      let task = JSON.parse(JSON.stringify(this.getTask));
      task.dates.isDone = this.isDone;

      task.dates.isDone = this.isDone;
      this.$emit("updatedTask", task);
    },
    saveEdit(task){
      this.$emit('saveEdit',task)
    },
    editDesc(){
      this.$emit('editDesc')
    },
    closeDescEdit(){
      this.$emit('closeDescEdit')
    },
    setCover(img){
      if(this.isCoverImage(img)==='Remove cover') this.$emit('setCover', '')
      else this.$emit('setCover',img)
    },
      isCoverImage(imgUrl){
        if(this.getTask.style.imgUrl === imgUrl) return 'Remove cover'
        else return 'Make cover'
      },
        attDate(attDate) {
      let date = new Date(attDate);
      if (!attDate) return;
      let shortMonth = date.toLocaleString("en-us", { month: "short" });
      let day = date.getDate();
      let year = date.getFullYear();
      let stringDate = `${shortMonth}  ${day}, ${year}`;
      return stringDate;
    },

  },
  computed: {
    getLabel() {
      let labels = [];
      if (!this.getTask.labelIds || !this.getTask.labelIds.length) return null;
      this.getBoard.labels.forEach((label) => {
        let isLabel = this.getTask.labelIds.some(
          (taskLabelId) => label.id === taskLabelId
        );
        if (isLabel) {
          labels.push(label);
        }
      });
      return labels;
    },
    getDates() {
      return this.getTask.dates;
    },
    
    startDate() {
      let date = new Date(this.getDates.startDate);
      if (
        !this.getDates.startDate ||
        this.getDates.dueDate === this.getDates.startDate
      )
        return;
      let shortMonth = date.toLocaleString("en-us", { month: "short" });
      let day = date.getDate();
      let stringDate = `${shortMonth} ${day} - `;
      return stringDate;
    },
    dueDate() {
      let date = new Date(this.getDates.dueDate);
      if (!this.getDates.dueDate) return;
      let shortMonth = date.toLocaleString("en-us", { month: "short" });
      let day = date.getDate();
      let year = date.getFullYear();
      let stringDate = `${shortMonth}  ${day}, ${year}`;
      return stringDate;
    },
       getTaskAttachments(){
      let taskAtts = []
      
      this.$store.getters.getCurrBoard.attachments.forEach((att)=>{
        if(att.task.id===this.getTask.id) taskAtts.push(att)
      })
      return taskAtts
      },
    
  },
  components:{
    taskDescription
  }
};
</script>