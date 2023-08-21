<template>
  <el-card shadow="always" :style="{ width: '500px' }">
    <el-form
      :model="controls"
      :rules="rules"
      ref="form"
      @submit.native.prevent="onSubmit"
    >
      <h2>Login to admin panel</h2>
      <el-form-item label="Login" prop="login">
        <el-input v-model.trim="controls.login" />
      </el-form-item>
      <div class="mb2">
        <el-form-item label="Password" prop="password">
          <el-input v-model.trim="controls.password" type="password" />
        </el-form-item>
      </div>
      <el-form-item>
        <el-button type="primary" round native-type="submit" :loading="loading">
          Login
        </el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script>
export default {
  layout: "empty",
  head: {
    title: `Admin panel login | ${process.env.appName}`
  },
  data() {
    return {
      loading: false,
      controls: {
        login: "",
        password: "",
      },
      rules: {
        login: [
          {
            required: true,
            message: "Enter login",
            trigger: "blur",
          },
        ],
        password: [
          {
            required: true,
            message: "Enter password",
            trigger: "blur",
          },
          {
            min: 6,
            message: "Password must be less than 6 characters",
            trigger: "blur",
          },
        ],
      },
    };
  },
  mounted() {
    const { message } = this.$route.query;

    switch (message) {
      case "login":
        this.$message.info("Login to get started");
        break;
      case "logout":
        this.$message.success("You have successfully logged out");
        break
      case "session":
        this.$message.warning("Session timed out, please login again");
        break
    }
  },
  methods: {
    onSubmit() {
      this.$refs.form.validate(async (valid) => {
        if (valid) {
          this.loading = true;

          try {
            const formData = {
              login: this.controls.login,
              password: this.controls.password,
            };
            await this.$store.dispatch("auth/login", formData);
            this.$router.push("/admin");
          } catch (e) {
            this.loading = false;
          }
        }
      });
    },
  },
};
</script>
