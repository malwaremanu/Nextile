<template>
  <div>
      <div class="flex items-center justify-between">
          <div class="flex items-center gap-1 text-sm text-center p-2">
              <span v-for="(p, index) in pallete" :key="index" style="width:40px;height:40px" 
              class="rounded-full border shadow-lg"
              :style="{ backgroundColor: p }" @click="choose_colour(index)"></span>
          </div>

          <div class="flex items-center gap-1 text-sm text-center p-2">
              <div>
                  <input type="text" @keydown="initializeCanvas" @keyup="initializeCanvas" v-model="numCols" class="d"/>
                  x <input type="text" @keydown="initializeCanvas" @keyup="initializeCanvas" v-model="numRows" class="d"/>
              </div>
              
              <div class="flex items-center gap-2">
                  <div type="color" class="outline-none rounded-full border shadow-lg" 
                  style="width:40px;height:40px" :style="{ backgroundColor: pallete[selectedColor] }"></div>
              </div>
              
          </div>
      </div>        

      <div class="p-2">
        <div class="flex items-center justify-between">
          <div>
            <button type="button" @click="download()">Download Data</button>
          </div>            
            
          <div>
              <input type="file" ref="fileInput" @change="handleFileUpload">
              <button @click="uploadFile">Upload</button> <button @click="loadData()">Load</button>
          </div>
        </div>        
      </div>
              
      <div id="canvas" class="p-2 mx-auto" style="display: grid;" :style="gridStyle">
        <div
            v-for="(block, index) in blocks"
            :key="index"
            class="block text-center text-xs text-gray-50"
            :style="{ backgroundColor: pallete[block] }"
            @click="fillBlock(index)"
          >
          {{ index + 1 }}
        </div>
      </div>        
  </div>
</template>

<script>
  export default {    
    data(){
      return {
          defaultcolor : "white",
          pallete : [
              '#ffffff', '#FF0000', '#00FF00', '#0000FF'
          ],
          selectedColor: 0,
          numRows: 5,
          numCols: 15,
          blocks: [],
          jsonData: null,
          fileToUpload: null,
      }
    },
    mounted() {
      this.initializeCanvas();
    },
    computed: {
      gridStyle() {
          return {
              gridTemplateColumns: `repeat(${this.numCols}, min-content)`,
          };
      },
  },
    methods: {
      download(){
          // Assuming your data is stored in this.dataToDownload
          const data = {
              pallete : this.pallete,
              numRows: this.numRows,
              numCols: this.numCols,
              blocks: this.blocks
          };

          // Convert the data to JSON format
          const jsonData = JSON.stringify(data, null, 2);

          // Create a Blob containing the JSON data
          const blob = new Blob([jsonData], { type: "application/json" });

          // Create a URL for the Blob
          const url = URL.createObjectURL(blob);

          // Create a link element to trigger the download
          const link = document.createElement("a");
          link.href = url;
          link.download = "data.json";

          // Append the link to the body and click it
          document.body.appendChild(link);
          link.click();

          // Clean up: remove the link and revoke the URL
          document.body.removeChild(link);
          URL.revokeObjectURL(url);
      },

      handleFileUpload(event) {
      // Get the selected file from the input
      this.fileToUpload = event.target.files[0];
      },
      
      loadData() {
          if (!this.jsonData) {
              alert("No JSON data loaded. Please upload JSON first.");
              return;
          }

          // Now you can use this.jsonData for further processing
          // For example, you might want to save it to a Vuex store, display it in a component, etc.
              console.log("Loaded JSON Data:");
              console.log("json data", this.jsonData.pallete)
              this.pallete = this.jsonData.pallete
              this.numRows = this.jsonData.numRows
              this.numCols = this.jsonData.numCols
              this.blocks = this.jsonData.blocks 
      },
      uploadFile() {
          if (!this.fileToUpload) {
              alert("Please select a file first.");
              return;
          }

          // Create a FileReader
          let reader = new FileReader();

          // Define what to do when the file is loaded
          reader.onload = (event) => {
              // Get the file content
              const fileContent = event.target.result;
              this.jsonData = JSON.parse(fileContent);
          };

          // Read the file as text
          reader.readAsText(this.fileToUpload);
      },
      
      choose_colour(p){
          this.selectedColor= p
      },
      initializeCanvas() {
          this.blocks = []
      for (let i = 0; i < this.numRows * this.numCols; i++) {
          this.blocks.push(0);
      }
      console.clear();
      },
      fillBlock(index) {           
          this.blocks[index] = this.selectedColor
      },
  },
};
</script>

<style>
  .block {
    width: 50px;
    height: 50px;
    border: 1px solid #000;
  }

  .custom-number-input input::-webkit-outer-spin-button,
  .custom-number-input input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }

  /* Override Firefox's default styles */
  .custom-number-input input[type=number] {
    -moz-appearance: textfield;
  }

  .d { 
  @apply bg-green-50 max-w-[50px] px-4 py-2 outline-none;
  }
</style>