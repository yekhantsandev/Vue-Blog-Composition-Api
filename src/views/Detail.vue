<template>
  <div v-if="error">
    {{ error }}
  </div>
  <div v-if="post" class="post">
    <h2>{{ post.title }}</h2>
    <p>{{ post.body }}</p>

    <button class="delete" @click="deletePost">delete</button>
  </div>
  <div v-else>
    <Spinner />
  </div>
</template>

<script>
import getPost from "../composables/getPost";
import Spinner from "../components/Spinner.vue";
import { useRoute } from "vue-router";
import { useRouter } from "vue-router";
import { db } from "../firebase/config";

export default {
  components: { Spinner },
  props: ["id"],
  setup(props) {
    let route = useRoute(); // this.$route
    let router = useRouter();
    let { post, error, load } = getPost(route.params.id);
    load();

    let deletePost = async () => {
      let id = props.id;
      await db.collection("posts").doc(id).delete();

      // redirect home page
      router.push({ name: "home" });
    };

    return { post, error, deletePost };
  },
};
</script>

<style>
.post {
  margin: 0 40px 30px;
  padding-bottom: 30px;
  border-bottom: 1px dashed #e7e7e7;
}
.post h2 {
  display: inline-block;
  position: relative;
  font-size: 26px;
  color: white;
  margin-bottom: 10px;
  max-width: 400px;
}
.post h2::before {
  content: "";
  display: block;
  width: 100%;
  height: 100%;
  background: #ff8800;
  position: absolute;
  z-index: -1;
  padding-right: 40px;
  left: -30px;
  transform: rotateZ(-1deg);
}

button.delete {
  margin: 30px auto;
}
</style>
