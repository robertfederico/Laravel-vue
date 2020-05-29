<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col-12">
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">Users List</h3>

            <div class="card-tools">
              <button type="button" class="btn btn-success" @click="newModal">
                <i class="fas fa-user-plus fa-fw"></i> Add New
                User
              </button>
            </div>
          </div>
          <div class="card-body table-responsive p-0">
            <table class="table table-hover text-nowrap">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Created At</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(user,counter) in users" :key="user.id">
                  <td>{{ ++counter }}</td>
                  <td>{{ user.name }}</td>
                  <td>{{ user.email }}</td>
                  <td>
                    <div v-if="user.type =='admin'">
                      <span class="badge badge-danger">{{ user.type | capitalize }}</span>
                    </div>
                    <div v-else>
                      <span class="badge badge-success">{{ user.type | capitalize }}</span>
                    </div>
                  </td>
                  <td>{{ user.created_at | myDate }}</td>
                  <td>
                    <a href="#" @click="editModal(user)" class="btn btn-primary btn-sm">
                      <i class="fas fa-edit"></i>
                    </a>
                    <a href="#" @click="deleteUser(user.id)" class="btn btn-danger btn-sm">
                      <i class="fas fa-trash"></i>
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <div class="modal fade" id="userModal" tabindex="-1" role="dialog">
      <div class="modal-dialog modal-dialog-centered modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">{{editMode ? 'Update User' : 'Add User'}}</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <form
            @submit.prevent="editMode ? updateUser() : createUser()"
            @keydown="form.onKeydown($event)"
          >
            <div class="modal-body">
              <alert-error :form="form"></alert-error>
              <div class="row">
                <div class="col-md-6">
                  <div class="form-group">
                    <input
                      v-model="form.name"
                      type="text"
                      name="name"
                      class="form-control"
                      placeholder="Enter Name"
                      :class="{'is-invalid': form.errors.has('name')}"
                    />
                    <has-error :form="form" field="name"></has-error>
                  </div>
                  <div class="form-group">
                    <input
                      v-model="form.email"
                      type="email"
                      name="email"
                      class="form-control"
                      placeholder="Enter Email"
                      :class="{'is-invalid': form.errors.has('email')}"
                    />
                    <has-error :form="form" field="email"></has-error>
                  </div>
                  <div class="form-group">
                    <select
                      v-model="form.type"
                      name="type"
                      class="form-control"
                      :class="{'is-invalid': form.errors.has('type')}"
                    >
                      <option value>Select User Role</option>
                      <option value="admin">Admin</option>
                      <option value="user">Standard User</option>
                      <option value="author">Author</option>
                    </select>
                    <has-error :form="form" field="type"></has-error>
                  </div>
                  <div class="form-group">
                    <input
                      v-model="form.password"
                      type="password"
                      name="password"
                      class="form-control"
                      placeholder="Enter password"
                      :class="{'is-invalid': form.errors.has('password')}"
                    />
                    <has-error :form="form" field="password"></has-error>
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="form-group">
                    <textarea
                      v-model="form.bio"
                      name="bio"
                      class="form-control"
                      rows="8"
                      cols="30"
                      placeholder="Short bio for user (optional)"
                      :class="{'is-invalid': form.errors.has('bio')}"
                    ></textarea>
                    <has-error :form="form" field="bio"></has-error>
                  </div>
                </div>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
              <button v-show="editMode" type="submit" class="btn btn-success">Update User</button>
              <button
                v-show="!editMode"
                type="submit"
                :disabled="form.busy"
                class="btn btn-primary"
              >Create User</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      counter: 0,
      editMode: true,
      users: {},
      form: new Form({
        id: "",
        name: "",
        email: "",
        password: "",
        type: "",
        bio: "",
        photo: ""
      })
    };
  },
  methods: {
    newModal() {
      this.form.reset();
      this.form.clear();
      this.editMode = false;
      $("#userModal").modal("show");
    },
    editModal(user) {
      this.form.reset();
      this.form.clear();
      this.editMode = true;
      $("#userModal").modal("show");
      this.form.fill(user);
    },
    loadUsers() {
      axios.get("api/user").then(({ data }) => (this.users = data.data));
    },
    createUser() {
      this.$Progress.start();
      this.form
        .post("api/user")
        .then(response => {
          // Fire this event after user is created
          Fire.$emit("actionCompleted");
          $("#userModal").modal("hide");

          Toast.fire({
            icon: "success",
            title: "User created successfully"
          });

          this.$Progress.finish();
        })
        .catch(response => {
          this.$Progress.fail();
          Toast.fire({
            icon: "warning",
            title: "There were some problems with your input"
          });
        });
    },
    deleteUser(id) {
      Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, delete it!"
      }).then(result => {
        if (result.value) {
          this.form
            .delete("api/user/" + id)
            .then(() => {
              Swal.fire({
                title: "Deleted",
                text: "User is deleted Successfully",
                icon: "success",
                onClose: () => {
                  Fire.$emit("actionCompleted");
                }
              });
            })
            .catch(() => {
              Toast.fire({
                icon: "error",
                title: "Smething went wrong."
              });
            });
        }
      });
    },
    updateUser(id) {
      this.$Progress.start();
      this.form
        .put("api/user/" + this.form.id)
        .then(() => {
          Fire.$emit("actionCompleted");
          $("#userModal").modal("hide");
          Toast.fire({
            icon: "success",
            title: "User information has been updated successfully!"
          });
          this.$Progress.finish();
        })
        .catch(() => {
          this.$Progress.fail();
          Toast.fire({
            icon: "error",
            title: "There were some problems with your input"
          });
        });
    }
  },
  created() {
    this.loadUsers();
    // listen to the event that was triggred after creater user
    Fire.$on("actionCompleted", () => {
      this.loadUsers();
    });
  }
};
</script>
