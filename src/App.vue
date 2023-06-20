<template>
  <div class="w-full h-screen bg-gradient-to-b from-[#a9c23f] to-white p-6 justify-between text-center">
    <div class="w-full md:w-4/5 xl:w-2/3 mx-auto bg-white">
      <div class="p-6">
        <img alt="Verra Mobility" src="/src/assets/half_logo.jpg" class="w-32" />
      </div>
    </div>
    <div v-if="!verified" class="w-full md:w-4/5 xl:w-1/2 mx-auto my-20">
      <div class="bg-white rounded-2xl p-12">
        <h1 class="text-xl font-semibold pb-10">Please enter your email to start watching!</h1>
        <input class="w-1/2 border rounded p-2" type="email" v-model="email" placeholder="jane.doe@gmail.com">
        <br>
        <p v-if="error != ''" class="text-red-500 mt-2">Error: {{ error }}</p>
        <br>
        <button class="mt-2 rounded border border-[#a9c23f] py-2 px-4 hover:bg-[#a9c23f] hover:text-white transition" @click="submitEmail">
          <span v-if="loading">Loading...</span>
          <span v-else>Start Viewing</span>
        </button>
      </div>
    </div>
    <div v-else class="w-full md:w-4/5 xl:w-1/2 mx-auto my-20">
      <mux-player stream-type="live:dvr" playback-id="wcmWkV5UiFS701Z3i00g2tGMMHQyidyrpXx8jIGEWBgHY" metadata-video-title="Verra.live" primary-color="#a9c23f" metadata-viewer-user-id="Verra.live" target-live-window="Infinity"></mux-player>
    </div>
    <p>Produced with ♥ by Broadcast Rentals</p>
  </div>
</template>

<script setup lang="ts">
import {ref} from "vue";
import axios from "axios";

const email = ref<string>("");
const verified = ref<boolean>(false);
const loading = ref<boolean>(false);
const error = ref<string>("");


const validEmail = ():boolean => {
  return email.value.includes("@");
}

const submitEmail = async ():Promise<void> => {
  if (!validEmail()) {
    error.value = "Please enter a valid email address";
    return;
  }
  try {
    loading.value = true;
    const data = await axios.post("https://verra.live/api/email", {email: email.value})
    // const data = await axios.post("http://localhost:3000/api/email", {email: email.value})
    if(data.status == 200){
      error.value = "";
      verified.value = true;
    } else {
      error.value = "There was an error submitting your email. Please try again.";
      return;
    }
  } catch (e) {
    error.value = "There was an error submitting your email. Please try again.";
    return;
  } finally {
    loading.value = false;
  }
}

const IncrementTime = async (): Promise<void> => {
  try {
    const data = await axios.post("https://verra.live/api/increment", {email: email.value})
    // const data = await axios.post("http://localhost:3000/api/increment", {email: email.value})
    if(data.status == 200){
      return;
    } else {
      console.log("Error logging your time.");
      return;
    }
  } catch (e) {
    console.log("Error logging your time.");
    return;
  }
}

//Run incrementTime every 5 minutes
setInterval(IncrementTime, 300000);
</script>


<style scoped>
</style>
