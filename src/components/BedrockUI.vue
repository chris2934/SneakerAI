<template>
  <div class="container">
    <h1>Ask AWS Bedrock</h1>

    <!-- Textarea to input the prompt -->
    <label for="prompt">Your Question:</label>
    <textarea
        id="prompt"
        v-model="prompt"
        placeholder="Type your question here..."
        rows="4"
        cols="50"
        class="text-input"
    ></textarea>

    <!-- Submit Button -->
    <button
        @click="handleSubmit"
        :disabled="loading"
        class="submit-button"
    >
      {{ loading ? "Loading..." : "Submit" }}
    </button>

    <!-- Result Display -->
    <div class="response">
      <h2>Response:</h2>
      <p v-if="error" class="error">{{ error }}</p>
      <p v-else>{{ response }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      // User input for the AI model (prompt)
      prompt: "",
      // Response from the backend
      response: "",
      // Loading state for submission
      loading: false,
      // Error message
      error: "",
    };
  },
  methods: {
    async handleSubmit() {
      // Prevent submission if no prompt is provided
      if (!this.prompt.trim()) {
        this.error = "Please enter a prompt.";
        return;
      }

      this.loading = true;
      this.response = ""; // Clear previous response
      this.error = ""; // Clear previous errors

      try {
        // Send the prompt to the backend
        const res = await axios.post("http://localhost:5000/api/bedrock", {
          prompt: this.prompt,
        });

        // Extract response from backend
        this.response = res.data.response.outputText || "No response received";
      } catch (error) {
        console.error("Error communicating with the backend:", error);
        this.error = "Error: Unable to get a response from the server.";
      } finally {
        this.loading = false; // Stop loading state
      }
    },
  },
};
</script>

<style scoped>
.container {
  width: 80%;
  margin: 50px auto; /* Center container */
  text-align: center;
}

label {
  display: block;
  margin-bottom: 8px;
  font-size: 18px;
  font-weight: bold;
  text-align: left; /* Align label text to left */
}

.text-input {
  width: 100%;
  padding: 8px;
  margin-bottom: 12px; /* Space between input and button */
  box-sizing: border-box;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.submit-button {
  align-self: flex-end; /* Align to the far right */
  padding: 10px 20px;
  background-color: grey;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  margin-top: 10px;
  cursor: pointer;
}

.submit-button:disabled {
  background-color: gray;
  cursor: not-allowed;
}

.submit-button:hover:enabled {
  background-color: darkblue;
}

.response {
  margin-top: 30px;
  text-align: left;
}

.error {
  color: red;
}
</style>