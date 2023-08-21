<template>
  <el-form
    :model="controls"
    :rules="rules"
    ref="form"
    @submit.native.prevent="onSubmit"
  >
    <h1 class="mb">Create new post</h1>
    <el-form-item label="Enter post title" prop="title">
      <el-input v-model="controls.title" />
    </el-form-item>
    <el-form-item label="Text in .md or .html format" prop="text">
      <el-input
        v-model="controls.text"
        type="textarea"
        resize="none"
        :rows="10"
      />
    </el-form-item>
    <el-button class="mb" type="success" plain @click="previewDialog = true">
      Preview
    </el-button>
    <el-dialog title="Preview" :visible.sync="previewDialog">
      <div :key="controls.text">
        <vue-markdown>{{ controls.text }}</vue-markdown>
      </div>
    </el-dialog>
    <el-upload
      class="mb"
      drag
      ref="upload"
      action="https://jsonplaceholder.typicode.com/posts/"
      :on-change="handleImageChange"
      :auto-upload="false"
    >
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">
        Drag image <em>or click</em>
      </div>
      <div class="el-upload__tip" slot="tip">files with jpg/png extension</div>
    </el-upload>
    <el-form-item>
      <el-button type="primary" round native-type="submit" :loading="loading">
        Create post
      </el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  layout: "admin",
  middleware: ["admin-auth"],
  head: {
    title: `New post | ${process.env.appName}`
  },
  data() {
    return {
      image: null,
      previewDialog: false,
      loading: false,
      controls: {
        title: "",
        text: "",
      },
      rules: {
        title: [
          {
            required: true,
            message: "Post title cannot be empty",
            trigger: "blur",
          },
        ],
        text: [
          {
            required: true,
            message: "Text cannot be empty",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    onSubmit() {
      this.$refs.form.validate(async (valid) => {
        if (valid && this.image) {
          this.loading = true;

          const formData = {
            title: this.controls.title,
            text: this.controls.text,
            image: this.image,
          };

          try {
            await this.$store.dispatch("post/create", formData);
            this.controls.title = "";
            this.controls.text = "";
            this.image = null;
            this.$refs.upload.clearFiles()
            this.$message.success("Post created");
            this.loading = false;
          } catch (e) {
          } finally {
            this.loading = false;
          }
        } else {
          this.$message.warning("Form is not valid");
        }
      });
    },
    handleImageChange(file, fileList) {
      this.image = file.raw;
    },
  },
};
</script>
<style lang="scss" scoped>
form {
  width: 600px;
}
</style>
