<template>
  <v-row align="center" class="list px-3 mx-auto">
    <v-col cols="12" md="8">
      <v-text-field v-model="title" label="Search by Title"></v-text-field>
    </v-col>

    <v-col cols="12" md="4">
      <v-btn small @click="searchTitle">
        Search
      </v-btn>
    </v-col>

    <v-col cols="12" sm="12">
      <v-card class="mx-auto" tile>
        <v-card-title>Courses List</v-card-title>

        <v-data-table
          :headers="headers"
          :items="courses"
          disable-pagination
          :hide-default-footer="true"
        >
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon small class="mr-2" @click="editCourse(item.id)">mdi-pencil</v-icon>
            <v-icon small @click="deleteCourse(item.id)">mdi-delete</v-icon>
          </template>
        </v-data-table>

        <v-card-actions v-if="courses.length > 0">
          <v-btn small color="error" @click="removeAllCourses">
            Remove All
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import CourseDataService from "../services/CourseDataService";
export default {
  name: "courses-list",
  data() {
    return {
      courses: [],
      title: "",
      headers: [
        { text: "Title", align: "start", sortable: false, value: "title" },
        { text: "Description", value: "description", sortable: false },
        { text: "Status", value: "status", sortable: false },
        { text: "Actions", value: "actions", sortable: false },
      ],
    };
  },
  methods: {
    retrieveCourses() {
      CourseDataService.getAll()
        .then(response => {
          this.courses = response.data.map(this.getDisplayCourse);
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },

    refreshList() {
        this.retrieveCourses();
    },

    removeAllCourses() {
      CourseDataService.deleteAll()
        .then(response => {
            this.courses = [];
            console.log(response.data);
            this.refreshList();
        })
        .catch((e) => {
          console.log(e);
        });
    },

    searchTitle() {
      CourseDataService.findByTitle(this.title)
        .then((response) => {
          this.courses = response.data.map(this.getDisplayCourse);
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },

    editCourse(id) {
      this.$router.push({ name: "course-details", params: { id: id } });
    },

    deleteCourse(id) {
      CourseDataService.delete(id)
        .then(() => {
          this.refreshList();
        })
        .catch((e) => {
          console.log(e);
        });
    },

    getDisplayCourse(course) {
      return {
        id: course.id,
        title: course.title.length > 30 ? course.title.substr(0, 30) + "..." : course.title,
        description: course.description.length > 30 ? course.description.substr(0, 30) + "..." : course.description,
        status: course.animated ? "Animated" : "Suspended",
      };
    },
  },
  mounted() {
    this.retrieveCourses();
  },
};
</script>

<style>
.list {
  max-width: 750px;
}
</style>