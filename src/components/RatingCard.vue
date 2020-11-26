<template>
  <div class="card-wrapper">
    <div v-if="asyncStatus === 'loading'">
      <div class="lds-spinner">
        <div class="lds-dual-ring"></div>
      </div>
    </div>
    <div class="rating-test"></div>
    <div v-if="asyncStatus === 'resolved'">
      <div class="rating-card">
        <div class="card-header">
          <div class="rating-section">
            <div class="left">
              <h4 class="rating-title">Overall Rating</h4>
              <div class="rating">
                <div class="rating-value highlited">5</div>
                <div class="rating-stars">
                  <span class="highlited" v-for="star in stars" :key="star">
                    &#9733;
                  </span>
                </div>
                {{ overallRating }} Votes
              </div>
            </div>
            <a href="#" class="plus-button">+</a>
          </div>
        </div>
        <div class="card-body">
          <div class="rating-section">
            <div class="thumb">
              <img :src="thumbnail" alt="" />
            </div>
            <div class="right">
              <h4 class="rating-title">{{ name }}</h4>
              <div class="rating">
                <div class="rating-stars">
                  <span
                    :class="currentRating >= star ? 'highlited' : ''"
                    @click="setRating(star)"
                    v-for="star in stars"
                    :key="star"
                  >
                    &#9733;
                  </span>
                </div>
              </div>
            </div>
          </div>
          <p class="description">
            <strong>Email: </strong> {{ userData.email }} <br />
            <strong>Phone: </strong> {{ userData.phone }}
          </p>
          <p>From: {{ userData.dob.date | formatDate }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "RatingCard",
  data() {
    return {
      asyncStatus: "",
      userData: [],
      stars: [1, 2, 3, 4, 5],
      defaultRating: 0,
      currentRating: 0,
    };
  },
  computed: {
    name() {
      return this.userData.name.first + " " + this.userData.name.last;
    },
    thumbnail() {
      return this.userData.picture.thumbnail;
    },
    overallRating() {
      return this.userData.dob.age;
    },
  },
  filters: {
    formatDate: (d) => {
      let date = new Date(d);
      return (
        date.getDate() + "-" + (date.getMonth() + 1) + "-" + date.getFullYear()
      );
    },
  },
  methods: {
    async getData() {
      this.asyncStatus = "loading";
      try {
        let response = await fetch("https://randomuser.me/api");
        let parceData = await response.json();
        this.asyncStatus = "resolved";
        this.userData = parceData.results[0];
        this.defaultRating = parceData.results[0].dob.age;
      } catch (error) {
        this.asyncStatus = "error";
      }
    },
    setRating(val) {
      this.currentRating = val;
      this.userData.dob.age = this.defaultRating + val;
    },
  },
  mounted() {
    this.getData();
  },
};
</script>
