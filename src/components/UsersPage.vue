<template>
  <v-container>
    <H1> List of Users </H1>
    <v-row>
      <v-col cols="3">
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-col>
      <v-col cols="2">
        <v-select
          v-model="sortBy"
          :items="sortByList"
          label="Sort by"
        ></v-select>
      </v-col>
      <v-col cols="1">
        <v-select
          v-model="sortDirection"
          :items="sortDirectionList"
          label="Sort Direction"
        ></v-select>
      </v-col>
      <v-col cols="2">
        <v-btn
          color="primary"
          class="mr-4"
          @click="clearFilter"
        >
          Clear Filter
        </v-btn>
      </v-col>
      <v-col cols="2">
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="success"
              class="mr-4"
              v-bind="attrs"
              v-on="on"
            >
              Add User
            </v-btn>
          </template>
          <v-container 
            style="background-color: white !important;"
          >
            <UserForm
              :isEdit="false"
              @success="onSuccess"
              @error="onError"
            />
          </v-container>
        </v-dialog>
      </v-col>
    </v-row>
    <v-data-table
      :headers="tableHeaders"
      :items="users"
      disable-initial-sort
      hide-default-footer
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
      </template>
    </v-data-table>
    <v-row class="mt-5">
      <v-col cols="3" sm="6" md="4">
        <v-select
          v-model="rowsPerPage"
          :items="rowsPerPageList"
          label="Rows per page"
        ></v-select>
      </v-col>
      <v-col cols="3" sm="6" md="4">
        <v-pagination
          v-model="page"
          :length="totalPage"
          circle
          color="primary"
          @input="getUsers"
        />
      </v-col>
    </v-row>
    <v-dialog 
      v-model="successModal.show"
      max-width="500px"
    >
      <SuccessModal
        :message="successModal.message"
        @close="successModal.show = false"        
      />
    </v-dialog>
    <v-dialog 
      v-model="errorModal.show"
      max-width="500px"
    >
      <ErrorModal
        :message="errorModal.message"
        @close="errorModal.show = false"
      />
    </v-dialog>
    <v-dialog 
      v-if="editModal"
      v-model="editModal"
      max-width="500px"
    >
      <v-container 
        style="background-color: white !important;"
      >
        <UserForm
          :isEdit="true"
          :user="selectedUser"
          @success="onSuccesEdit"
          @error="onErrorEdit"
        />
      </v-container>
    </v-dialog>
    <v-dialog 
      v-if="deleteModal"
      v-model="deleteModal.show"
      max-width="500px"
    >
      <ConfirmationModal
        :message="deleteModal.message"
        @confirm="deleteUser"
        @close="deleteModal.show = false"
      />
    </v-dialog>
  </v-container>
</template>

<script>
  import axios from 'axios';
  import { API_URL } from '../utils/const'
  import UserForm from './UserForm.vue';
  import SuccessModal from './modals/SuccessModal.vue';
  import ErrorModal from './modals/ErrorModal.vue';
  import ConfirmationModal from './modals/ConfirmationModal.vue';

  export default {
    name: 'UsersPage',
    components: {
      UserForm,
      SuccessModal,
      ErrorModal,
      ConfirmationModal
    },

    data: () => ({
      users: [
        {
          "id": 1,
          "name": "string",
          "gender": "MALE",
          "dob": "2023-04-11T14:51:26.772+00:00",
          "address": "string",
          "email": "string",
          "userRole": {
            "id": 1,
            "name": "QA"
          }
        },
        {
          "id": 2,
          "name": "string15",
          "gender": "MALE",
          "dob": "2023-04-11T14:51:26.772+00:00",
          "address": "string",
          "email": "string",
          "userRole": {
            "id": 1,
            "name": "QA"
          }
        },
        {
          "id": 8,
          "name": "azhar1",
          "gender": "MALE",
          "dob": "2000-09-18T00:00:00.000+00:00",
          "address": "sleman",
          "email": "azharmail.com",
          "userRole": {
            "id": 5,
            "name": "BACKEND"
          }
        },
        {
          "id": 11,
          "name": "string1",
          "gender": "MALE",
          "dob": "2023-04-11T14:34:33.395+00:00",
          "address": "string",
          "email": "string",
          "userRole": {
            "id": 1,
            "name": "QA"
          }
        },
        {
          "id": 12,
          "name": "string2",
          "gender": "MALE",
          "dob": "2023-04-11T14:34:33.395+00:00",
          "address": "string",
          "email": "string",
          "userRole": {
            "id": 1,
            "name": "QA"
          }
        }
      ],
      tableHeaders: [
        {
          text: 'No',
          value: 'no',
          sortable: false
        },
        {
          text: 'Name',
          value: 'name',
          sortable: false
        },
        {
          text: 'Gender',
          value: 'gender',
          sortable: false
        },
        {
          text: 'Date of Birth',
          value: 'formattedDob',
          sortable: false
        },
        {
          text: 'Address',
          value: 'address',
          sortable: false
        },
        {
          text: 'Email',
          value: 'email',
          sortable: false
        },
        {
          text: 'Role',
          value: 'userRole.name',
          sortable: false
        },
        {
          text: 'Action',
          value: 'actions',
          sortable: false
        }
      ],
      sortByList: [
        { text: "Name", value: "name"},
        { text: "date of birth", value: "dob"},
        { text: "address", value: "address"},
        { text: "email", value: "email"}
      ],
      sortDirectionList: [
        "ASC", "DESC"
      ],
      sortBy: '',
      sortDirection: '',
      gender: ["MALE", "FEMALE"],
      rowsPerPageList: [5, 10, 20, 30],
      search: '',
      page: 1,
      rowsPerPage: 10,
      totalPage: 0,
      dialog: false,
      successModal: {
        show: false,
        message: ''
      },
      errorModal: {
        show: false,
        message: ''
      },
      editModal: false,
      selectedUser: {},
      deleteModal: {
        show: false,
        message: ''
      }
    }),
    mounted() {
      this.getUsers();
    },
    watch: {
      sortBy: function() {
        this.getUsers();
      },
      sortDirection: function() {
        this.getUsers();
      },
      search: function() {
        this.page = 1;
        this.getUsers();
      },
      rowsPerPage: function() {
        this.page = 1;
        this.getUsers();
      }
    },
    methods: {
      getUsers() {
        axios.get(API_URL 
          + '/user/?orderBy=' + this.sortBy 
          + '&direction=' + this.sortDirection 
          + '&search=' + this.search
          + '&page=' + this.page
          + '&size=' + this.rowsPerPage
          )
          .then(response => {
            this.users = response.data.content.map((user) => {
              return {
                ...user,
                formattedDob: this.formatDate(user.dob),
                dob: this.rawDob(user.dob),
                no: response.data.content.indexOf(user) + 1 + (this.page - 1) * this.rowsPerPage
              }
            })
            this.totalPage = response.data.totalPages;
            console.log(response.data.content)
          })
          .catch(error => {
            console.log(error);
          })
      },
      clearFilter() {
        this.sortBy = '';
        this.sortDirection = '';
        this.search = '';
      },
      rawDob(date) {
        let d = new Date(date);
        let month = '' + (d.getMonth() + 1);
        let day = '' + d.getDate();
        let year = d.getFullYear();

        if (month.length < 2) 
          month = '0' + month;
        if (day.length < 2) 
          day = '0' + day;

        return [year, month, day].join('-');
      },
      formatDate(date) {
        let d = new Date(date);
        let month = '' + (d.getMonth() + 1);
        let day = '' + d.getDate();
        let year = d.getFullYear();

        if (month.length < 2) 
          month = '0' + month;
        if (day.length < 2) 
          day = '0' + day;

        return [day, month, year].join('-');
      },
      onSuccess() {
        this.dialog = false
        this.successModal.show = true;
        this.successModal.message = 'User has been added successfully';
        this.getUsers();
      },
      onError() {
        this.dialog = false
        this.errorModal.show = true;
        this.errorModal.message = 'Failed to add user';
      },
      editItem(user) {
        this.selectedUser = user;
        this.editModal = true;
      },
      onSuccesEdit() {
        this.editModal = false;
        this.successModal.show = true;
        this.successModal.message = 'User has been edited successfully';
        this.getUsers();
      },
      onErrorEdit() {
        this.editModal = false;
        this.errorModal.show = true;
        this.errorModal.message = 'Failed to edit user';
      },
      deleteItem(user) {
        this.selectedUser = user;
        this.deleteModal.show = true;
        this.deleteModal.message = 'Are you sure want to delete ' + user.name + '?';
      },
      deleteUser() {
        axios.delete(API_URL + '/user/' + this.selectedUser.id)
          .then(() => {
            this.deleteModal.show = false;
            this.successModal.show = true;
            this.successModal.message = 'User has been deleted successfully';
            this.getUsers();
          })
          .catch(() => {
            this.deleteModal.show = false;
            this.errorModal.show = true;
            this.errorModal.message = 'Failed to delete user';
          })
      },
    }
  }
</script>
