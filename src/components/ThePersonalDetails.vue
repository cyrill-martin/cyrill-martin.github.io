<template>
  <div class="row">
    <div class="col-8">
      <h1>{{ fullName }}</h1>
      <div v-if="jobTitle">{{ jobTitle }}</div>
      <div class="summary">
        {{ summary }}
      </div>
      <div class="socials">
        <span class="social-network">
          <a :href="`mailto:${contact.eMail}`">E-Mail</a>
        </span>
        <span
          v-for="network in socialNetwork"
          :key="network.network"
          class="social-network"
        >
          <a :href="network.url" target="_blank">{{ network.network }}</a>
        </span>
      </div>
    </div>
    <div class="col-4 image-container">
      <img :src="imageLink" alt="Photo of Cyrill Martin" />
    </div>
  </div>
  <div class="row">
    <div class="col-12">
      <span v-html="smallPrint"></span>
    </div>
  </div>
</template>

<script>
export default {
  props: [
    "fullName",
    "image",
    "jobTitle",
    "summary",
    "contact",
    "socialNetwork",
  ],
  computed: {
    imageLink() {
      return require(`../assets/images/${this.image}`);
    },
    smallPrint() {
      return `Please write an <a href="mailto:${this.contact.eMail}">e-mail</a> to get the PDF version of my CV including personal details and references.`;
    },
  },
};
</script>

<style scoped>
img {
  width: 100%;
  max-width: 280px;
}

.summary {
  font-size: 1.1rem;
  margin: 1rem 0;
}

.social-network + .social-network::before {
  content: " · ";
}

.socials {
  font-size: 1.1rem;
  margin-bottom: 2rem;
}

.image-container {
  text-align: center;
}

@media only screen and (min-width: 45em) {
  img {
    max-width: 350px;
  }

  .summary {
    font-size: 1.3rem;
    margin: 3rem 0 2rem 0;
  }

  .socials {
    font-size: 1.2rem;
  }

}
</style>
