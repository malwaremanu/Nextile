<template>
    <div>
    <div class="flex flex-row h-full mt-[100px] gap-2 justify-center">
      <div class="min-w-[400px] border rounded-lg overflow-hidden shadow-lg">
        <div class="px-6 py-4">
          <div class="font-bold text-xl mb-2">Recipe Generator</div>
          <div class="mb-4">
            <label class="block text-gray-700 text-sm font-bold mb-2">Ingredients</label>
            <input  v-model="r.ingredients" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="ingredients" type="text" placeholder="Enter ingredients">
          </div>
          <div class="mb-4">
            <label class="block text-gray-700 text-sm font-bold mb-2" >Meal Type</label>
            <select v-model="r.mealType" class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline" id="mealType">
              <option value="Breakfast">Breakfast</option>
              <option value="Lunch">Lunch</option>
              <option value="Dinner">Dinner</option>
              <option value="Snack">Snack</option>
            </select>
          </div>
          <div class="mb-4">
            <label class="block text-gray-700 text-sm font-bold mb-2" >Cuisine Preference</label>
            <input v-model="r.cuisine" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline" id="cuisine" type="text" placeholder="e.g., Italian, Mexican">
          </div>
          <div class="mb-4">
            <label class="block text-gray-700 text-sm font-bold mb-2" >Cooking Time</label>
            <select v-model="r.cookingTime" class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline" id="cookingTime">
              <option value="Less than 30 minutes">Less than 30 minutes</option>
              <option value="30-60 minutes">30-60 minutes</option>
              <option value="More than 1 hour">More than 1 hour</option>
            </select>
          </div>
          <div class="mb-4">
            <label class="block text-gray-700 text-sm font-bold mb-2">Complexity</label>
            <select v-model="r.complexity" class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline" id="complexity">
              <option value="Beginner">Beginner</option>
              <option value="Intermediate">Intermediate</option>
              <option value="Advanced">Advanced</option>
            </select>
          </div>
          <div class="px-6 py-4">
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" @click="fetch2()" type="button">Generate Recipe</button>                      
          </div>
        </div>
      </div>
      <div class="w-[600px] text-lg h-[565px] text-xs text-gray-600 p-4 border rounded-lg shadow-xl whitespace-pre-line overflow-y-auto" v-html="result"></div>
    </div>
  </div>
</template>

<script>
export default {    
    data(){
        return {
            r : {
                ingredients : 'Rice, Dal, Ghee',
                mealType : 'Breakfast',
                cuisine : 'Indian',
                cookingTime : 'Less than 30 minutes',
                complexity : 'Beginner',

            },
            result : ''
        }
    },
    mounted() {
    // Load Showdown.js script dynamically
        const script = document.createElement('script');
        script.src = 'https://cdnjs.cloudflare.com/ajax/libs/showdown/1.9.1/showdown.min.js';
        script.onload = () => {
        // Script loaded successfully, initialize Showdown
        this.converter = new showdown.Converter();
        };
        document.body.appendChild(script);
        this.result = '<img src="https://cdnl.iconscout.com/lottie/premium/thumb/ai-robot-saying-hello-5647513-4703496.gif">'
    },
    methods : {
        sendRecipePrompt() {
            var rr = this.r
            var api_key = "AIzaSyDql8W0JubQqOr2v0B8r0kGeuXbY0us9N8"

            prompt = "Please provide a small recipe, including steps for preparation and cooking. Only use the ingredients provided which are " + rr.ingredients + ". The recipe should highlight the fresh and vibrant flavors of the ingredients. The meal type should be " + rr.mealType + ". This meal should take cooking time of " + rr.cookingTime + ". The complexity of this recepie should be of a " + rr.ingredients + " level. The Cuisine Preference is " + rr.cuisine + ". Also, give the recipe a suitable name in its local language based on cuisine preference. Do not use more than 100 words."
            const url = 'https://g.manupal.dev/' + prompt; // Update the URL with the actual endpoint

            // Fetch POST request
            fetch(url)
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                console.log('Response from server:', data);
            })
            .catch(error => {
                console.error('There was a problem with the fetch operation:', error);
            });
        },
        fetch2(){
            console.log("Started AI")
            this.result = '<img src="https://cdnl.iconscout.com/lottie/premium/thumb/ai-reading-books-5647517-4703500.gif" />'
            var rr = this.r
            var api_key = "AIzaSyDql8W0JubQqOr2v0B8r0kGeuXbY0us9N8"

            prompt = "Please provide a small recipe, including steps for preparation and cooking. Only use the ingredients provided which are " + rr.ingredients + ". The recipe should highlight the fresh and vibrant flavors of the ingredients. The meal type should be " + rr.mealType + ". This meal should take cooking time of " + rr.cookingTime + ". The complexity of this recepie should be of a " + rr.ingredients + " level. The Cuisine Preference is " + rr.cuisine + ". Also, give the recipe a suitable name in its local language based on cuisine preference. Do not use more than 100 words. Please share HTML formatted text only, do not share markdown format."            

            fetch('https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=' + api_key, {
            method: 'POST',
            headers: {
            'Content-Type': 'application/json'
            },
            body: JSON.stringify({
            contents: [{
            parts: [{
            text: prompt
            }]
            }]
            })
            })
            .then(response => response.json())
            .then(data => {
                console.log(data); // Handle the response data here
                this.rr = data.candidates[0].content.parts[0].text
                console.log("got response")
                const converter = new showdown.Converter();
                this.htmlOutput = converter.makeHtml(this.rr);
                console.log("converted to HTML")
                this.result = this.htmlOutput
                console.log("yeah")

            })
            .catch(error => {
            console.error('Error:', error);
            });
        }
    }   
}
</script>