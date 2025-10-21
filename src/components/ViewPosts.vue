<script setup>
    import axios from 'axios';
</script>

<script>
export default {
    data() {
        return {
            moods: ["Happy", "Sad", "Angry"],
            posts: [], // array of post objects
            entry: "",
            mood: "",
            showEditPost: false,
            editPostId: "",
        }
    },
    computed: {
        baseUrl() {
            if (window.location.hostname=='localhost')
                return 'http://localhost:3000' 
            else {
                const codespace_host = window.location.hostname.replace('5173', '3000')
                return `https://${codespace_host}`;
            }
        }
    },
    created() { // created is a hook that executes as soon as Vue instance is created
        axios.get(`${this.baseUrl}/posts`)
            .then(response => {
                // this gets the data, which is an array, and pass the data to Vue instance's posts property
                this.posts = response.data
            })
            .catch(error => {
                this.posts = [{ entry: 'There was an error: ' + error.message }]
            })
    },
    methods: {
        editPost(id) {
            this.showEditPost = true;
            const selectedPost = this.posts.find(post => post.id === id);
            if (selectedPost) {
                this.entry = selectedPost.entry;
                this.mood = selectedPost.mood;
                this.editPostId = selectedPost.id;
            }
        },
        updatePost(event) {
        // Prevent form submission from reloading the page
        event.preventDefault();

        // Create the updated post object
        const updatedPost = {
            id: this.editPostId,
            entry: this.entry,
            mood: this.mood,
        };

        // Send the PUT request to the server to update the post
        axios.post(`${this.baseUrl}/updatePost`, updatedPost)
            .then(response => {
                // Find the index of the updated post in the posts array
                const index = this.posts.findIndex(post => post.id === this.editPostId);

                // If the post exists, update it in the posts array with the response data
                if (index !== -1) {
                    this.$set(this.posts, index, response.data); // Use $set to ensure reactivity
                }

                // Hide the edit form after the post is updated
                this.showEditPost = false;

                // Optionally, clear the form fields after submission
                this.entry = '';
                this.mood = '';
            })
            .catch(error => {
                console.error('Error updating the post:', error);
            });
        }
    }
}
</script>

<template>
    <div id="demo">
        <h2> View Blog Posts </h2>
        <table class="table m-2">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Entry</th>
                    <th>Mood</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in posts">
                    <td>{{ post.id }}</td>
                    <td>{{ post.entry }}</td>
                    <td>{{ post.mood }}</td>
                    <td><button @click=editPost(post.id)>Edit</button></td>
                </tr>
            </tbody>

        </table>
        <!-- TODO Show form for editing post only when edit button is clicked -->
        <div id="editPost" v-if="showEditPost">
            <h3>Edit Post</h3>
            <div id="postContent" class="mx-3">
                <form @submit =updatePost>
                    <div class="mb-3">
                        <label for="entry" class="form-label">Entry</label>
                        <textarea id="entry" class="form-control" v-model="entry" required></textarea>
                    </div>
                    <div class="mb-3">
                        <label for="mood" class="form-label">Mood</label>
                        <select id="mood" class="form-select" v-model="mood" required>
                            <option value="" disabled>Select Mood</option>
                            <option v-for="mood in moods" :value="mood">{{ mood }}</option>
                        </select>
                    </div>
                    <button type="submit" class="btn btn-primary" >Update Post</button>
                </form>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
