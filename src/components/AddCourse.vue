<template>
  <div class="submit-form mt-3 mx-auto">
    <p class="headline">Add Course</p>

    <div v-if="!submitted">
      <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="course.title"
          :rules="[(v) => !!v || 'Title is required']"
          label="Title"
          required
        ></v-text-field>

        <v-text-field
          v-model="course.description"
          :rules="[(v) => !!v || 'Description is required']"
          label="Description"
          required
        ></v-text-field>
      </v-form>

      <v-btn color="primary" class="mt-3" @click="saveCourse">Submit</v-btn>
    </div>

    <div v-else>
      <v-card class="mx-auto">
        <v-card-title>
          Submitted successfully!
        </v-card-title>

        <v-card-subtitle>
          Click the button to add new Course.
        </v-card-subtitle>

        <v-card-actions>
          <v-btn color="success" @click="newCourse">Add</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>
import CourseDataService from "../services/CourseDataService";

export default {
  name: "add-course",
  data() {
    return {
      course: {
        id: null,
        title: "",
        description: "",
        animated: false,
      },
      submitted: false,
    };
  },
  methods: {
    saveCourse() {
      var data = {
        title: this.course.title,
        description: this.course.description,
      };

      CourseDataService.create(data)
        .then((response) => {
          this.course.id = response.data.id;
          console.log(response.data);
          this.submitted = true;
        })
        .catch((e) => {
          console.log(e);
        });
    },

    newCourse() {
      this.submitted = false;
      this.course = {};
    },
  },
};
</script>

<style>
.submit-form {
  max-width: 300px;
}
</style>