<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col-12">
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">Users List</h3>

            <div class="card-tools">
              <button
                type="button"
                class="btn btn-success"
                data-toggle="modal"
                data-target="#userModal"
              >
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
                <tr v-for="user in users" :key="user.id">
                  <td>{{ user.id }}</td>
                  <td>{{ user.name }}</td>
                  <td>{{ user.email }}</td>
                  <td>
                    <span class="badge badge-success">{{ user.type }}</span>
                  </td>
                  <td>{{ user.created_at }}</td>
                  <td>
                    <a href="#" class="btn btn-primary btn-sm">
                      <i class="fas fa-edit"></i>
                    </a>
                    <a href="#" class="btn btn-danger btn-sm">
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
            <h5 class="modal-title" id="exampleModalLabel">Add User</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <form @submit.prevent="createUser" @keydown="form.onKeydown($event)">
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
              <button type="submit" :disabled="form.busy" class="btn btn-primary">Create User</button>
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
      users: {},
      form: new Form({
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
    loadUsers() {
      axios.get("api/user").then(({ data }) => (this.users = data.data));
    },
    createUser() {
      this.form.get("api/user");
    }
  },
  created() {
    this.loadUsers();
  }
};
</script>
