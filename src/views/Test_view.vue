<template>
 <v-container>
   
    <!--container-->
<section class="container">

	<!--questionBox-->
	<div class="questionBox" id="app">

			<!--qusetionContainer-->
      <v-card>
         <!-- {{quiz.questions.length}}
         {{questionIndex}}
         {{quiz.questions.length}} -->
        <div >
          Total score: {{ score() }} / {{ quiz.questions.length }}
          {{questionIndex}} : {{quiz.questions.length}} 
          <v-alert>Complete Quiz</v-alert>
        </div>

			<div v-if="questionIndex<quiz.questions.length" v-bind:key="questionIndex">
 
				<header>
					<!--progress-->
					<div>
            <h1 class="title is-6">Quiz</h1>
						<progress class="progress is-info is-small" :value="(questionIndex/quiz.questions.length)*100" max="100">{{(questionIndex/quiz.questions.length)*100}}%</progress>
						<p>{{(questionIndex/quiz.questions.length)*100}}% complete</p>
					</div>
					<!--/progress-->
				</header>

            {{quiz.questions.length}}
            {{quiz}}
		<!-- questionTitle -->
     <v-alert
      class="ma-2"
      border="right"
      color="blue-grey"
      dark
    >
     	<h2 class="titleContainer title">{{questionIndex+1}}){{ quiz.questions[questionIndex].question }}</h2>
    </v-alert>
			<!-- {{quiz.questions[0].ans[0]}} -->


				<div class="optionContainer">
					<div class="option" v-for="(ans, index) in quiz.questions[questionIndex].ans" @click="selectOption(index)" :class="{ 'is-selected': userResponses[questionIndex] == index}" :key="index">
						{{ index | charIndex }}. {{ ans.ans }}
					</div>
				</div>

				<!-- quizOptions -->
				<!-- <div class="optionContainer">
					<div class="option" v-for="(response, index) in quiz.questions[questionIndex].responses"  :class="{ 'is-selected': userResponses[questionIndex] == index}" :key="index">
                  <v-btn  :class="{ 'is-selected': userResponses[questionIndex] == index}" class="ma-1" @click="selectOption(index)" >{{ index | charIndex }} ) {{ question.text }}</v-btn>
					</div>
				</div> -->

            
            <v-btn class="mr-1" v-on:click="prev();" :disabled="questionIndex < 1" > Back</v-btn>       

            <v-btn v-on:click="next();"  :disabled="userResponses[questionIndex]==null" > NEXT </v-btn>


			</div>
      </v-card>
			<!--/questionContainer-->
	</div>
	<!--/questionBox-->
  </section>
 </v-container> 

</template>

<script>
import api from "@/services/api";

export default {
 async  mounted () {
    var quiz_data_id =  this.quizdata;
    this.quizdata = await api.getquizShow({quiz_data_id}); 

    this.quiz = {
      questions: this.quizdata
   },
   this.userResponses = Array(this.quiz.questions.length).fill(null);
  },  
  data() {
    return {
      quiz: null,
      questionIndex: 0,
      userResponses: "",
      isActive: false,
      quizdata:null
    };
   },
   filters: {
      charIndex: function(i) {
         return String.fromCharCode(97 + i);
      }
   },
   methods: {
		 restart: function() {
			 	this.questionIndex=0;
		 		this.userResponses=Array(this.quiz.questions.length).fill(null);
       },
       selectOption: function(index) {
          this.$set(this.userResponses, this.questionIndex, index)
         //console.log(this.userResponses);
       },
       next: function() {
         if (this.questionIndex < this.quiz.questions.length)
            this.questionIndex++;
       },
       prev: function() {
         if (this.quiz.questions.length > 0) this.questionIndex--;
       },
       score: function() {
         var score = 0;
         for (let i = 0; i < this.userResponses.length; i++) {
            if (
               typeof this.quiz.questions[i].ans[
                  this.userResponses[i]
               ] !== "undefined" &&
               this.quiz.questions[i].ans[this.userResponses[i]].correct
            ) {
               score = score + 1;
            }
         }
         return score;
         //return this.userResponses.filter(function(val) { return val }).length;
      }
   }
}
</script>

<style>
 .is-selected{
  background-color: #4CAF50;
  }

</style>