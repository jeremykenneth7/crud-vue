<template>
  <div class="edit-story">
    <h1>Edit Story</h1>
    <form @submit.prevent="saveChanges">
      <div>
        <label for="title">Title:</label>
        <input type="text" id="title" v-model="story.title" required>
      </div>
      <div>
        <label for="author">Author:</label>
        <input type="text" id="author" v-model="story.author" required>
      </div>
      <div>
        <label for="category">Category:</label>
        <input type="text" id="category" v-model="story.category" required>
      </div>
      <div>
        <label for="tags">Tags (comma separated):</label>
        <input type="text" id="tags" v-model="tagInput">
        <button type="button" @click="addTag">Add Tag</button>
        <div v-if="story.tags.length > 0">
          <span v-for="(tag, index) in story.tags" :key="index" class="tag">{{ tag }}</span>
        </div>
      </div>
      <div>
        <label for="status">Status:</label>
        <select id="status" v-model="story.status" required>
          <option value="Draft">Draft</option>
          <option value="Publish">Publish</option>
        </select>
      </div>
      <div>
        <button type="submit">Save Changes</button>
      </div>
    </form>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";

export default {
  name: "EditStory",
  setup() {
    const story = reactive({
      _id: '',
      title: '',
      author: '',
      category: '',
      tags: [],
      status: ''
    });

    const tagInput = ref('');

    const fetchStory = async () => {
      try {
        const response = await axios.get(`https://us-central1-fullstack-api-38a4f.cloudfunctions.net/api/api/stories/${$route.params.id}`);
        Object.assign(story, response.data);
      } catch (error) {
        console.error('Error fetching story:', error);
      }
    };

    const saveChanges = async () => {
      try {
        await axios.put(`https://us-central1-fullstack-api-38a4f.cloudfunctions.net/api/api/stories/${story._id}`, story);
        router.push('/'); // Assuming 'router' is imported from Vue Router
      } catch (error) {
        console.error('Error saving story changes:', error);
      }
    };

    const addTag = () => {
      if (tagInput.value) {
        story.tags.push(tagInput.value.trim());
        tagInput.value = '';
      }
    };

    onMounted(fetchStory);

    return {
      story,
      tagInput,
      saveChanges,
      addTag
    };
  }
};
</script>

