<template>
  <div>
    <el-form
      :model="controls"
      :rules="rules"
      ref="form"
      @submit.native.prevent="onSubmit"
    >
      <h1>Add a comment</h1>
      <el-form-item label="Your name" prop="name">
        <el-input v-model="controls.name" />
      </el-form-item>
      <el-form-item label="Comment text" prop="text">
        <el-input
          type="textarea"
          v-model="controls.text"
          resize="none"
          :rows="2"
        />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" round native-type="submit" :loading="loading">
          Add a comment
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  props: {
    postId: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      loading: false,
      controls: {
        name: "",
        text: "",
      },
      rules: {
        name: [
          {
            required: true,
            message: "Name cannot be empty",
            trigger: "blur",
          },
        ],
        text: [
          {
            required: true,
            message: "Enter your comment",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    onSubmit() {
      this.$refs.form.validate(async  (valid) => {
        if (valid) {
          this.loading = true;

          const formData = {
            name: this.controls.name,
            text: this.controls.text,
            postId: this.postId,
          };

          try { 
            const newComment = await this.$store.dispatch('comment/create', formData)
            this.$message.success("Comment added");
            this.$emit("created", newComment);
          } catch (e) {
            this.loading = false;
          }
        }
      });
    },
  },
};
</script>

<style lang="scss" scoped></style>
