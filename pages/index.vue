<template>
  <div>
    <!-- TheExperience component with z-index 0 for the background -->
    <TheExperience class="z-0" />

    <!-- The div with the heading centered and z-index 10 -->
    <div
      class="drop-shadow-2xl z-10 font-index font-extrabold"
      :style="{
        position: 'absolute',
        top: `${transitionTop}%`,
        left: '50%',
        transform: 'translate(-50%, -50%)',
      }"
    >
      <h1 class="text-center text-6xl md:text-8xl text-indigo-900">
        Welcome to my 3D Journey!
      </h1>
      <div class="flex flex-col pt-8 md:pt-14 items-center">
        <div class="text-3xl">
          <NuxtLink
            target="_blank"
            to="https://www.linkedin.com/in/aysegulkaradan/"
            class="mx-2 text-gray-50 hover:text-indigo-900"
            alt="linkedin"
            aria-label="connect with linkedin"
          >
            <Icon
              name="uil:linkedin"
              class="text-3xl sm:text-5xl lg:text-7xl"
            />
          </NuxtLink>
          <NuxtLink
            target="_blank"
            to="mailto:karadan.ay7@gmail.com"
            class="mx-2 text-gray-50 hover:text-indigo-900"
            alt="mailto"
            aria-label="connect with mail"
          >
            <Icon
              name="uil:envelope"
              class="text-3xl sm:text-5xl lg:text-7xl"
            />
          </NuxtLink>
          <NuxtLink
            target="_blank"
            alt="github"
            to="https://github.com/karadanay7"
            class="mx-2 text-gray-50 hover:text-indigo-900"
            aria-label="connect with github"
          >
            <Icon name="uil:github" class="text-3xl sm:text-5xl lg:text-7xl" />
          </NuxtLink>

          <h2
            class="text-2xl md:text-3xl lg:text-4xl text-gray-50 text-center m-3 hover:text-indigo-900"
          >
            Aysegul Karadan
          </h2>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const transitionTop = ref(0);

// Function to calculate transitionTop value
function calculateTransitionTop(elapsedTime) {
  // Adjust the transition duration as needed
  const transitionDuration = 1000; // 1 second

  // Calculate the transitionTop value from top to center
  transitionTop.value =
    (Math.min(transitionDuration, elapsedTime) / transitionDuration) *
    (100 / 2);
}

// Start the transition calculation using setInterval
let startTime = Date.now();
const interval = 16; // 60 frames per second

const intervalId = setInterval(() => {
  const currentTime = Date.now();
  const elapsedTime = currentTime - startTime;

  if (elapsedTime < 1000) {
    // If still within the transition duration
    calculateTransitionTop(elapsedTime);
  } else {
    // Transition complete
    transitionTop.value = 100 / 2;
    clearInterval(intervalId);
  }
}, interval);
</script>

<style scoped>
/* Add any necessary styles here */
</style>
