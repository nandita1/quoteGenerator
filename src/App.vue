<template>
  <div class="container">
    <blockquote class="quote-box">
      <p class="quotation-mark">
        “
      </p>
      <p class="quote-text">
        {{ quote.en }}
      </p>
      <hr />
      <div class="blog-post-actions">
        <p class="blog-post-bottom pull-left">
          {{ quote.author }}
        </p>
        <p class="blog-post-bottom pull-right">
          <i
            class="fa fa-star-o"
            @click="check(0)"
            :class="checked[0] ? 'checked' : ''"
            aria-hidden="true"
          ></i>
          <i
            class="fa fa-star-o"
            @click="check(1)"
            :class="checked[1] ? 'checked' : ''"
            aria-hidden="true"
          ></i>
          <i
            class="fa fa-star-o"
            @click="check(2)"
            :class="checked[2] ? 'checked' : ''"
            aria-hidden="true"
          ></i>
          <i
            @click="check(3)"
            class="fa fa-star-o"
            :class="checked[3] ? 'checked' : ''"
            aria-hidden="true"
          ></i>
          <i
            @click="check(4)"
            class="fa fa-star-o"
            :class="checked[4] ? 'checked' : ''"
            aria-hidden="true"
          ></i>
        </p>
      </div>
    </blockquote>
  </div>
</template>

<script>
  import stringSimilarity from "string-similarity";
  export default {
    data() {
      return {
        quotes: [],
        quote: "",
        checked: [false, false, false, false, false],
        indexOfQuote: 0,
      };
    },
    created() {
      fetch("https://programming-quotes-api.herokuapp.com/quotes/lang/en")
        .then((response) => response.json())
        .then((response) => {
          this.quotes = response;
          var random = Math.floor(Math.random() * response.length);
          this.quote = response[random];
          this.indexOfQuote = random;
          var checked = [false, false, false, false, false];
          for (var j = 0; j < Math.round(this.quote.rating); j++) {
            checked[j] = true;
          }
          this.checked = [...checked];
        });
    },
    methods: {
      check(i) {
        fetch("https://programming-quotes-api.herokuapp.com/quotes/vote", {
          method: "post",
          body: JSON.stringify({ quoteId: this.quote.id, newVote: i }),
        }).then((response) => {
          console.log(response);
        });
        if (i >= 3) {
          var new_quotes = [...this.quotes];
          new_quotes.splice(this.indexOfQuote, 1);
          var matches = stringSimilarity.findBestMatch(
            this.quote.en,
            new_quotes.map((a) => a.en)
          );
          //console.log(matches.bestMatch.target);
          this.quote = this.quotes.find((obj) => {
            return obj.en === matches.bestMatch.target;
          });
          let checked = [false, false, false, false, false];
          for (let j = 0; j < Math.round(parseInt(this.quote.rating)); j++) {
            checked[j] = true;
          }
          this.checked = [...checked];
        } else {
          var random = Math.floor(Math.random() * this.quotes.length);
          this.quote = this.quotes[random];
          this.indexOfQuote = random;
          let checked = [false, false, false, false, false];
          for (let j = 0; j < Math.round(parseInt(this.quote.rating)); j++) {
            checked[j] = true;
          }
          this.checked = [...checked];
        }
      },
    },
  };
</script>

<style>
  .container {
    margin: 0;
    height: 100vh;
    padding: 0;
    width: 100%;
    background: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.3)),
      url("./assets/night-view.jpg");
    background-size: cover;
    background-position: center;
  }
  blockquote {
    border-left: none;
    margin: 250px auto;
  }

  .quote-box {
    overflow: hidden;
    padding-top: -100px;
    border-radius: 17px;
    background-color: white;
    color: black;
    width: 400px;
    box-shadow: 2px 2px 2px 2px #e0e0e0;
  }

  .quotation-mark {
    margin-top: -10px;
    font-weight: bold;
    font-size: 100px;
    color: gray;
    font-family: "Times New Roman", Georgia, Serif;
  }

  .quote-text {
    font-size: 19px;
    margin-top: -65px;
  }

  i {
    cursor: pointer;
  }

  .checked {
    color: orange;
  }
</style>
