<template>
  <div v-if="currentCourse" class="edit-form py-3">
    <p class="headline">Edit Course</p>

    <v-form ref="form" lazy-validation>
      <v-text-field
        v-model="currentCourse.title"
        :rules="[(v) => !!v || 'Title is required']"
        label="Title"
        required
      ></v-text-field>

      <v-text-field
        v-model="currentCourse.description"
        :rules="[(v) => !!v || 'Description is required']"
        label="Description"
        required
      ></v-text-field>

      <label><strong>Status:</strong></label>
      {{ currentCourse.animated ? "Animated" : "Suspended" }}

      <v-divider class="my-5"></v-divider>

      <v-btn v-if="currentCourse.animated"
        @click="updateAnimated(false)"
        color="primary" small class="mr-2"
      >
        Suspend
      </v-btn>

      <v-btn v-else
        @click="updateAnimated(true)"
        color="primary" small class="mr-2"
      >
        Animate
      </v-btn>

      <v-btn color="error" small class="mr-2" @click="deleteCourse">
        Delete
      </v-btn>

      <v-btn color="success" small @click="updateCourse">
        Update
      </v-btn>
    </v-form>

    <p class="mt-3">{{ message }}</p>
  </div>

  <div v-else>
    <p>Please click on a Course...</p>
  </div>
</template>

<script>
import CourseDataService from "../services/CourseDataService";

export default {
  name: "course",
  data() {
    return {
      currentCourse: null,
      message: "",
    };
  },
  methods: {
    getCourse(id) {
      CourseDataService.get(id)
        .then((response) => {
          this.currentCourse = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },

    updateAnimated(status) {
      var data = {
        id: this.currentCourse.id,
        title: this.currentCourse.title,
        description: this.currentCourse.description,
        animated: status,
      };

      CourseDataService.update(this.currentCourse.id, data)
        .then((response) => {
          this.currentCourse.animated = status;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },

    updateCourse() {
      CourseDataService.update(this.currentCourse.id, this.currentCourse)
        .then((response) => {
          console.log(response.data);
          this.message = "The Course was updated successfully!";
        })
        .catch((e) => {
          console.log(e);
        });
    },

    deleteCourse() {
      CourseDataService.delete(this.currentCourse.id)
        .then((response) => {
          console.log(response.data);
          this.$router.push({ name: "courses" });
        })
        .catch((e) => {
          console.log(e);
        });
    },
  },
  mounted() {
    this.message = "";
    this.getCourse(this.$route.params.id);
  },
};
</script>

<style>
.edit-form {
  max-width: 300px;
  margin: auto;
}
</style>