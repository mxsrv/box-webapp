<template>
  <v-container fluid grid-list-md>
    <v-row no-gutters>
      <v-col>
        <v-card
          class="mx-auto pa-4"
          outlined
          title
          width="900px"
          height="900px"
          elevation="10"
        >
          <div class="first-row">
            <span
              style="
                font-size: 25px;
                font-family: Arial;
                color: rgb(0, 0, 0);
                background-color: transparent;
                padding: 10px;
              "
            >
              Available Tasks
            </span>
            <v-spacer />
            <v-dialog v-model="taskDialog" persistent max-width="600px">
              <template #activator="{ on, attrs }">
                <v-btn color="green lighten-1" dark v-bind="attrs" v-on="on">
                  Add a Task
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="text-h5"
                    >Click on a specific task and press "Add Task"</span
                  >
                </v-card-title>
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12">
                        <v-select
                          v-model="taskSelect"
                          :hint="`${taskSelect.title}`"
                          :items="taskTypes"
                          item-text="title"
                          item-value="abbr"
                          label="Select"
                          persistent-hint
                          return-object
                          single-line
                          color="green lighten-1"
                        ></v-select>
                      </v-col>
                    </v-row>
                  </v-container>
                  <small>*indicates required field</small>
                </v-card-text>
                <v-card-actions>
                  <v-spacer />
                  <v-btn
                    color="green lighten-1"
                    text
                    @click="taskDialog = false"
                  >
                    Close
                  </v-btn>
                  <v-btn color="green lighten-1" text @click="addTask">
                    Add Task
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </div>
          <v-divider class="mb-2"></v-divider>
          <v-card elevation="10" height="300px">
            <v-simple-table height="300px">
              <template #default>
                <thead>
                  <tr>
                    <th class="text-left">Title</th>
                    <th class="text-left">Description</th>
                    <th class="text-left">Delete Task</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in tasks" :key="item.id">
                    <td>{{ item.title }}</td>
                    <td>{{ item.description }}</td>
                    <td>
                      <v-btn
                        color="green lighten-1"
                        class="white--text"
                        elevation="2"
                        @click="deleteTask(item.id)"
                        >Delete</v-btn
                      >
                    </td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card>
          <div class="first-row">
            <span
              style="
                font-size: 25px;
                font-family: Arial;
                color: rgb(0, 0, 0);
                background-color: transparent;
                padding: 10px;
              "
            >
              Temperature Plot
            </span>
          </div>
          <v-divider class="ma-2"></v-divider>
          <v-card elevation="10" height="412px">
            <v-img
              alt="No temperature data for today"
              class="shrink mr-2"
              contain
              src="http://10.3.141.1:8000/api/getTemperaturePlot"
              max-height="700"
              max-width="700"
            />
          </v-card>
        </v-card>
      </v-col>
      <v-col>
        <v-card
          class="mx-auto pa-4"
          outlined
          title
          width="900px"
          height="900px"
          elevation="10"
        >
          <div class="first-row">
            <span
              style="
                font-size: 25px;
                font-family: Arial;
                color: rgb(0, 0, 0);
                background-color: transparent;
                padding: 10px;
              "
            >
              Data on Sensorbox
            </span>
            <v-spacer />
            <v-file-input
              v-if="!chosenFile"
              label="Add a File"
              outlined
              v-model="chosenFile"
            >
            </v-file-input>
            <v-btn
              color="green lighten-1"
              elevation="2"
              class="white--text"
              @click="uploadFile"
              v-if="chosenFile"
            >
              upload
              <v-icon right color="white"> mdi-cloud-upload </v-icon></v-btn
            >
          </div>
          <v-divider />
          <div class="first-row">
            <span
              style="
                font-size: 22px;
                font-family: Arial;
                color: rgb(0, 0, 0);
                background-color: transparent;
                padding: 10px;
              "
            >
              Files
            </span>
          </div>
          <v-divider class="mb-2"></v-divider>
          <v-card elevation="10" height="700px">
            <v-simple-table height="700px">
              <template #default>
                <thead>
                  <tr>
                    <th class="text-left">Filename</th>
                    <th class="text-left">More Info</th>
                    <th class="text-left">Image</th>
                    <th class="text-left">Arrived</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in files" :key="item.index">
                    <td>{{ item.filename }}</td>
                    <td>
                      <v-btn
                        color="green lighten-1"
                        text
                        @click="$set(infoDialog, item.index, true)"
                      >
                        Info</v-btn
                      >
                      <v-dialog
                        v-model="infoDialog[item.index]"
                        scrollable
                        max-width="500"
                        :key="item.index"
                      >
                        <v-card>
                          <v-card-title>
                            <span> Details about {{ item.filename }}</span>
                          </v-card-title>
                          <v-card-text>
                            <v-container>
                              <v-row
                                >Content Type: {{ item.contentType }}</v-row
                              >
                              <v-row
                                >Number of Downloads:
                                {{ item.downloads }}</v-row
                              >
                              <v-row>Upload Date: {{ item.uploadDate }}</v-row>
                              <v-row
                                >Number of Chunks: {{ item.chunksNum }}</v-row
                              >
                            </v-container>
                          </v-card-text>
                          <v-card-actions>
                            <v-btn
                              color="green lighten-1"
                              text
                              @click.stop="$set(infoDialog, item.index, false)"
                            >
                              Close
                            </v-btn>
                          </v-card-actions>
                        </v-card>
                      </v-dialog>
                    </td>
                    <td>
                      <v-img
                        :src="
                          'http://10.3.141.1:8000/api/getGridfsImage/' +
                          item.id
                        "
                        alt="No Image"
                        height="234px"
                        width="354px"
                      ></v-img>
                    </td>
                    <td>
                      <v-icon v-if="item.arrived" color="green lighten-1">
                        mdi-check
                      </v-icon>
                      <v-icon v-else color="red"> mdi-window-close </v-icon>
                    </td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card>
        </v-card>
      </v-col>
    </v-row>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ snackbarText }}

      <template #action="{ attrs }">
        <v-btn
          color="green lighten-1"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
import axios from 'axios'
var _ = require('lodash')
export default {
  data () {
    return {
      tasks: [],
      files: [],
      chunks: [],
      images: [],
      arrivedFiles: [],
      boxRawIP: '10.3.141.1:8000',
      chosenFile: null,
      taskDialog: false,
      infoDialog: {},
      title: '',
      description: '',
      item: {},
      taskTypeNum: { type: 1 },
      taskSelect: { title: 'Label a photo*' },
      taskTypes: [
        { title: 'Label a photo*' },
        { title: 'Take a photo*' },
        { title: 'Clean the box*' }
      ],
      /**
       * Label task snackbar params
       */
      snackbar: false,
      snackbarText: 'Task could not be loaded. No Images available to lable',
      timeout: 4000
    }
  },

  created () {
    this.getTasks()
    this.getFiles()
  },
  /**
   * Reloads files every second and tasks every 10 ms
   */
  mounted: function () {
    this.$nextTick(function () {
      window.setInterval(() => {
        this.getFiles()
      }, 1000)
    })
    this.$nextTick(function () {
      window.setInterval(() => {
        this.getTasks()
      }, 10)
    })
  },

  watch: {
    $route: 'getTasks'
  },

  methods: {
    /**
     * lets the users choose a file from their file system and upload it to the backend
     */
    uploadFile () {
      if (!this.chosenFile) {
        console.log('File Error')
      }
      const file = new FormData()

      file.append('sensor', this.chosenFile, this.chosenFile.name)
      axios
        .post('http://' + this.boxRawIP + '/api/writeData', file, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        })
        .then((res) => {
          console.log(res)
          this.chosenFile = null
          this.chunks = []
          this.getFiles()
          this.getChunks()
        })
    },

    /**
     * Lets the user choose a certain task type and adds it to the database
     */
    addTask () {
      if (_.isEqual(this.taskSelect, { title: 'Label a photo*' })) {
        console.log('NICE0')
        this.taskTypeNum = { type: 1 }
      } else if (_.isEqual(this.taskSelect, { title: 'Take a photo*' })) {
        console.log('NICE1')
        this.taskTypeNum = { type: 2 }
      } else if (_.isEqual(this.taskSelect, { title: 'Clean the box*' })) {
        console.log('NICE2')
        this.taskTypeNum = { type: 3 }
      } else {
        console.log('NICE3')
        this.taskTypeNum = { type: 4 }
      }
      console.log(this.taskTypeNum)
      axios
        .post('http://' + this.boxRawIP + '/api/addTask', this.taskTypeNum, {
          headers: {
            'Content-Type': 'application/json'
          }
        })
        .then(async (res) => {
          if (res.status === 200) {
            console.log('Task added')
          } else {
            console.log('Something went wrong')
          }
          this.getTasks()
        })
        .catch(async () => {
          console.log('Could not add Task due to empty Image Collection')
          this.snackbar = true
        })
    },
    /**
     * Queries all tasks contained in the database
     */
    getTasks () {
      this.$http
        .get('http://' + this.boxRawIP + '/api/getTasks')
        .then(async (res) => {
          if (res.status === 200) {
            var i
            if (res.body.length !== this.tasks.length) {
              this.tasks = []
            }
            for (i = 0; i < res.body.length; i = i + 1) {
              if (!this.tasks.some((item) => item.id === res.body[i]._id)) {
                this.tasks.push({
                  id: res.body[i]._id,
                  title: res.body[i].title,
                  description: res.body[i].description
                })
              }
            }
          }
        })
    },
    /**
     * Deletes a certain task by taking a given id
     * @param id
     */
    deleteTask (id) {
      this.taskDelete = { _id: id }
      console.log('Deleting Task', this.taskDelete)
      axios
        .post('http://' + this.boxRawIP + '/api/deleteTask', this.taskDelete, {
          headers: {
            'Content-Type': 'application/json'
          }
        })
        .then(async (res) => {
          if (res.status === 200) {
            this.tasks = []
            this.getTasks()
          } else {
            console.log('Deletion unsuccessful')
          }
        })
    },
    /**
     * Queries all files with certain information contained in the database
     */
    getFiles () {
      this.$http
        .get('http://' + this.boxRawIP + '/api/arrivedAtBackend/0')
        .then(async (res) => {
          if (res.status === 200) {
            this.arrivedFiles = res.body
          }
        })
      this.$http
        .get('http://' + this.boxRawIP + '/api/listFiles')
        .then(async (res) => {
          if (res.status === 200) {
            var i
            for (i = 0; i < res.body.length; i = i + 1) {
              if (!this.files.some((item) => item.id === res.body[i]._id)) {
                this.files.push({
                  index: i,
                  id: res.body[i]._id,
                  filename: res.body[i].filename,
                  contentType: res.body[i].contentType,
                  downloads: res.body[i].downloads,
                  uploadDate: new Date(res.body[i].uploadDate).toLocaleString(),
                  chunksNum: Math.ceil(
                    res.body[i].length / res.body[i].chunkSize
                  ),
                  arrived: this.arrivedFiles.includes(res.body[i]._id)
                })
                console.log('Arrived: ' + this.files[i].arrived)
              } else {
                this.files[i].arrived = this.arrivedFiles.includes(
                  res.body[i]._id
                )
              }
            }
          }
        })
    }
  }
}
</script>

<style lang="scss" scoped>
.first-row {
  margin: 5px 0;
  display: flex;
  align-items: center;
}
</style>
