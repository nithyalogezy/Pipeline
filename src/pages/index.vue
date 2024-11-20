<template>
  <div>
    <v-container>
    <v-row>
      <!-- Main Row Divided into Pipeline Display and Add Pipeline Form -->
      <v-row>
        <!-- Pipeline Display Section (col-8) -->
        <v-col cols="12"  md="8 mt-10 elevation-1" >
          <h1 class="text-h6 font-weight-bold mb-4 ">Pipeline Management</h1>
          <v-card type="info" border="start" elevation="2" class="mb-4 pa-4 custom-card">
            Newly signed-up recruitment candidates will always be added to the first pipeline, while the last pipeline will be assigned when activating the recruitment candidates.
          </v-card>

          <!-- Pipeline Cards -->
          <v-row dense class="g-4">
    
    <VueDraggable
      v-model="pipelines" 
      :options="{ handle: '.handle-dots' }" 
      group="pipelines"
      animation="150"
       
    >
    <div class="d-flex flex-row flex-wrap">
      <v-col
        cols="12"
        md="6"
        v-for="(pipeline, index) in pipelines"
        :key="pipeline.id"
        class="py-4 px-2"
      >
        <v-card
          class="pipeline-card"
          elevation="2"
          :style="{
            backgroundColor: lightPipelineColor(pipeline.color),
            border: `1px solid ${pipeline.color}`,
          }"
        >
          <v-row no-gutters >
            <!-- Draggable Handle (Dots) -->
            <v-col cols="auto" class="d-flex align-center handle-dots">
              <v-icon class="mr-n2">tabler-dots-vertical</v-icon>
              <v-icon class="ml-n2">tabler-dots-vertical</v-icon>
            </v-col>

            <!-- Pipeline Content with Edit and Delete Icons -->
            <v-col>
              <v-row class="d-flex align-center dense mb-0">
                <span
                  class="applicants-badge ml-4"
                  style="padding: 4px; border-radius: 6px; background-color: white; color: black; font-size: 0.7rem; font-weight: 10; inset-block-start: -5px; margin-block-end: 4px;"
                >
                  3 Applicants
                </span>
                <v-spacer></v-spacer>
                <!-- Edit and Delete Icons -->
                <div class="d-flex justify-end action-icons">
                  <v-btn
                    icon
                    small
                    @click="editPipeline(index)"
                    class="mx-1"
                    style="border: 1px solid black; background-color: white !important; block-size: 30px; color: black !important; inline-size: 30px;"
                  >
                    <v-icon>tabler-pencil</v-icon>
                  </v-btn>

                  <v-btn
                    icon
                    small
                    @click="deletePipeline(index)"
                    class="mx-1"
                    style="border: 1px solid red; background-color: white !important; block-size: 30px; color: red !important; inline-size: 30px;"
                  >
                    <v-icon>tabler-trash</v-icon>
                  </v-btn>
                </div>
              </v-row>
              <v-row class="mt-n3">
                <v-col cols="auto">
                  <v-avatar
                    size="40"
                    :style="{ backgroundColor: pipeline.color }"
                    class="text-h5 font-weight-bold white--text ml-1"
                  >
                    {{ pipeline.initial }}
                  </v-avatar>
                  <span class="font-weight-bold ml-2">{{ pipeline.name }}</span>
                  <p class="text--secondary ml-2">{{ pipeline.description }}</p>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-card>
      </v-col></div>
    </VueDraggable>
  </v-row>
  </v-col>
<!-- Add Pipeline Section (col-4) -->
<v-col cols="12" md="4 mt-7">
        <v-card class="pa-5 mb-1 form-card" elevation="1">
          <h2 class="text-h6 font-weight-bold ms-2 mb-4">Add Pipeline</h2>
          <v-form @submit.prevent="addPipeline" :key="isEditing ? 'edit' : 'add'" ref="pipelineForm">
            <!-- Title Field -->
             
       <label class="field-label font-weight-medium ">
      Title <span class="required-asterisk">*</span>
    </label>
    <v-text-field
      v-model="newPipeline.name"
      placeholder="Enter pipeline title"
      type="text"
      :error-messages="formErrors.name"
      outlined
      
    ></v-text-field>

            <!-- Description Field -->
            <label class="field-label font-weight-medium  mt-4">
      Description <span class="required-asterisk">*</span>
    </label>
    <v-text-field
      v-model="newPipeline.description"      
      type="text"
     :error-messages="formErrors.description"
      outlined
      textarea
      rows="4"      
     style="block-size: 100px;"
       ></v-text-field> 
            <div class="my-1">
              <label class="font-weight-medium">
                Color <span class="required-asterisk ">*</span>
              </label>  
              <div class="color-options-container" >         
              <v-row no-gutters class="flex-nowrap ">
                <v-col cols="auto" v-for="(color, index) in colors" :key="index">
                  <v-btn 
                    :style="{ backgroundColor: color + ' !important', width: '32px', height: '32px', borderRadius: '4px',}"
                    class="mx-4 my-4 color-btn"
                    :elevation="newPipeline.color === color ? 6 : 2"
                    @click="newPipeline.color = color"
                    icon
                  ></v-btn>
                </v-col>
              </v-row>
            </div></div>
     
      <v-row class="d-flex justify-end mt-10 mb-4 action-buttons">              
              <v-btn color="secondary me-2 custom-cancel-button " @click="resetForm">Cancel</v-btn>
              <v-btn color="primary" type="submit" class="mr-2 custom-add-button">Add</v-btn>
            </v-row>
      
    </v-form>
  </v-card>
</v-col>
</v-row>
    </v-row>
  </v-container>
  </div>
</template>

<script>
 
import { VueDraggable } from 'vue-draggable-plus';
export default {
  components: {
    VueDraggable, 
  },
  data() {
    return {
      colors: ["#b39ddb", "#80deea", "#a5d6a7", "#ffcc80", "#ef9a9a"], 
      pipelines: [],
      newPipeline: {
        name: "",
        description: "",
        color: "",
      },
      isEditing: false,
      editingIndex: null,
      formErrors: {
      name: '',
      description: '',
    },
    };
  },
  computed: {
  lightPipelineColor() {
       return (color) => {
      const [r, g, b] = color.match(/\w\w/g).map((hex) => parseInt(hex, 16));
      const lightColor = `rgb(${Math.min(r + 50, 255)}, ${Math.min(g + 50, 255)}, ${Math.min(b + 50, 255)})`;
      return lightColor;
    };
  },
},
created() {
  this.loadPipelines();
},
  methods: {
    loadPipelines() {
    const storedPipelines = localStorage.getItem("pipelines");
    if (storedPipelines) {
      this.pipelines = JSON.parse(storedPipelines);
    }
  },
  savePipelines() {
    localStorage.setItem("pipelines", JSON.stringify(this.pipelines));
  },
  validateForm() {
    let isValid = true;  
    if (!this.newPipeline.name) {
      this.formErrors.name = 'Title is required';
      isValid = false;
    } else {
      this.formErrors.name = '';
    }    
    if (!this.newPipeline.description) {
      this.formErrors.description = 'Description is required';
      isValid = false;
    } else {
      this.formErrors.description = '';
    }

    return isValid;
  },
  
    addPipeline() {     
    if (this.validateForm()) { 
    const initial = this.newPipeline.name.charAt(0).toUpperCase();  
    const newId = this.pipelines.length > 0 ? this.pipelines[this.pipelines.length - 1].id + 1 : 1;
    if (this.isEditing && this.editingIndex !== null) {     
      this.pipelines[this.editingIndex] = {
        id: this.pipelines[this.editingIndex].id, 
        ...this.newPipeline,
        initial,
      };
    } else {      
      this.pipelines.push({
        id: newId,
        ...this.newPipeline,
        initial,
      });
    }
    this.savePipelines();
    console.log('Form successfully submitted');
    this.resetForm();
  }
  },
    resetForm() {      
      this.newPipeline = {
        name: "",
        description: "",
        color: "",
      };
      this.formErrors = {
      name: '',
      description: '',
    };
      this.isEditing = false;
      this.editingIndex = null;
    
    },  
    deletePipeline(index) {
      this.pipelines.splice(index, 1);
      this.savePipelines(); 
    },
    editPipeline(index) {
    const pipeline = this.pipelines[index];
    this.newPipeline = { ...pipeline }; 
    this.isEditing = true;
    this.editingIndex = index;
  },
    
    // dragStart(pipeline, index) {
    //   this.draggedPipeline = { pipeline, index };
    // },
    // dragEnd() {
    //   this.draggedPipeline = null;
    // },
    // drop(pipeline, dropIndex) {
    //   const { pipeline: dragged, index: draggedIndex } = this.draggedPipeline;
    //   if (draggedIndex !== dropIndex) {
    //     this.pipelines.splice(draggedIndex, 1);
    //     this.pipelines.splice(dropIndex, 0, dragged);
    //   }
    //   this.draggedPipeline = null;
    // },  
  },
};
</script>

<style scoped>
.pipeline-card {
  padding: 24px;
  max-block-size: 150px;
}

.handle-dots {
  cursor: grab;
  font-weight: bold;
  margin-inline-end: 8px;
}

.form-card {
  padding: 24px;
}

.v-btn {
  border-radius: 8px;
}

.v-card h3 {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
}

.color-options-container {
  padding: 8px;
  border: 1px solid lightgray;
  border-radius: 8px;
}

.color-btn {
  border-radius: 4px;
  block-size: 32px;
  inline-size: 32px;
}

.custom-card {
  border: 2px solid #5ff0f3;
  background-color: #c9f2ed;
  color: black;
  font-size: 0.8rem;
}

.v-card p {
  color: #6b7280;
}

.v-avatar {
  color: #fff;
  font-weight: 700;
}

.custom-add-button {
  background-color: #0e4470 !important;
  color: white;
}

.custom-cancel-button {
  background-color: #dcd8d8 !important;
  color: black !important;
  margin-inline-end: 8px;
}

.action-buttons .v-btn {
  padding: 8px 16px;
}

.v-card.pa-5.mb-1 {
  border-radius: 8px;
  background-color: #f5f5f5;
}

.required-asterisk {
  color: red;
}

.field-label {
  display: inline-block;
  font-weight: bold;
  margin-block-end: 4px;
}

.applicants-badge {
  padding: 6px 8px;
  border-radius: 6px;
  background-color: white;
  color: black;
  font-size: 0.8rem;
  font-weight: bold;
}
</style>
