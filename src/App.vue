<template>
  <div id="app">
    <div class="container">
      <div class="form-group">
        <label for="">JSON Input</label>
        <textarea class="form-control" rows="5" v-model="stringJSON"></textarea>
      </div>
      <button
        class="btn btn-success btn-sm btn-block mt-2"
        @click="onParseJSON"
      >
        Parse JSON
      </button>
      <p class="mt-3 mb-1">Form Render</p>
      <div class="card">
        <div class="card-body">
          <div v-if="isLoading">
            <div class="d-flex justify-content-center">
              <div class="spinner-border" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
          </div>

          <div v-if="form.length === 0 && !isLoading">
            <div class="d-flex justify-content-center">No Form Rendered</div>
          </div>

          <div v-if="form.length > 0">
            <div class="d-flex justify-content-center">
              <nav style="--bs-breadcrumb-divider: '>'" aria-label="breadcrumb">
                <ol class="breadcrumb">
                  <li
                    v-for="(input, index) in form"
                    :key="index"
                    class="breadcrumb-item"
                    :class="{ active: index === position }"
                    @click="onClickLink(index)"
                  >
                    {{ input.title }}
                  </li>
                </ol>
              </nav>
            </div>
            <div class="card mt-3">
              <div class="card-body">
                <div
                  v-for="(input, index) in activeForm"
                  :key="index"
                  class="form-group mb-3"
                >
                  <label for="">{{ input.attributes.label }}</label>
                  <input
                    v-if="
                      input.type === 'field-input' &&
                      input.attributes.type === 'email'
                    "
                    type="email"
                    class="form-control"
                  />

                  <input
                    v-if="
                      input.type === 'field-input' &&
                      input.attributes.type === 'number'
                    "
                    type="number"
                    class="form-control"
                  />

                  <input
                    v-if="
                      input.type === 'field-input' &&
                      input.attributes.type === 'date'
                    "
                    type="date"
                    class="form-control"
                  />

                  <input
                    v-if="
                      input.type === 'field-input' &&
                      input.attributes.type === 'time'
                    "
                    type="time"
                    class="form-control"
                  />

                  <input
                    v-if="
                      input.type === 'field-input' &&
                      input.attributes.type === undefined
                    "
                    type="text"
                    class="form-control"
                  />

                  <textarea
                    v-if="input.type === 'textarea-input'"
                    class="form-control"
                  >
                  </textarea>

                  <select
                    v-if="input.type === 'select-input'"
                    class="form-control"
                  >
                    <option value="">
                      {{ input.attributes.first_selection }}
                    </option>
                    <option
                      v-for="(option, index) in input.attributes.options"
                      :key="index"
                      :value="option"
                    >
                      {{ option }}
                    </option>
                  </select>

                  <div
                    v-if="input.type === 'html-static'"
                    v-html="input.attributes.content"
                  ></div>

                  <div
                    v-if="input.attributes.helper_text !== undefined"
                    class="form-text"
                  >
                    {{ input.attributes.helper_text }}
                  </div>
                </div>
                <div class="d-flex justify-content-between mt-4">
                  <button
                    @click="onBack"
                    class="btn btn-secondary btn-sm"
                    :disabled="position === 0"
                  >
                    Back Button
                  </button>
                  <button
                    v-if="position < form.length - 1"
                    @click="onContinue"
                    class="btn btn-secondary btn-sm"
                  >
                    Continue Button
                  </button>

                  <button
                    v-if="position === form.length - 1"
                    @click="onSubmit"
                    class="btn btn-secondary btn-sm"
                  >
                    Submit Form
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      stringJSON: "",
      form: [],
      activeForm: [],
      isLoading: false,
      position: 0,
    };
  },
  methods: {
    onParseJSON() {
      if (this.stringJSON !== "") {
        try {
          this.isLoading = true;
          this.form = JSON.parse(this.stringJSON);

          for (let index = 0; index < this.form.length; index++) {
            for (
              let index2 = 0;
              index2 < this.form[index].fields.length;
              index2++
            ) {
              if (this.form[index].fields[index2].type === "select-input") {
                if (
                  !Array.isArray(
                    this.form[index].fields[index2].attributes.options
                  )
                ) {
                  let splitArray =
                    this.form[index].fields[index2].attributes.options.split(
                      "\nS"
                    );
                  this.form[index].fields[index2].attributes.options =
                    splitArray;
                }
              }
            }
          }

          this.activeForm = this.form[this.position].fields;
          this.isLoading = false;
        } catch (error) {
          alert("Invalid Format JSON/Array");
        }
      } else {
        alert("Please fill the JSON Input first!");
      }
    },
    onContinue() {
      this.position = this.position + 1;
      this.activeForm = this.form[this.position].fields;
    },
    onBack() {
      this.position = this.position - 1;
      this.activeForm = this.form[this.position].fields;
    },
    onClickLink(id) {
      this.position = id;
      this.activeForm = this.form[id].fields;
    },
    onSubmit() {
      alert("Form Submited");
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

.btn-block {
  width: 100%;
}

.breadcrumb-item.active {
  color: #000 !important;
  font-weight: 600;
}

.breadcrumb-item {
  cursor: pointer;
}
</style>
