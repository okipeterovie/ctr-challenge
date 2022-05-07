<template>
  <div class="container" style="max-width: 600px; height: auto">
    <div class="main">
      <!-- Heading -->
      <h2 class="text-center mt-5">CTR-Test</h2>
      <!-- Input -->

      <div class="outer mt-2" @click="allocate()">
        <div class="input-container">
          <div class="position-div">
            <input
              type="text"
              :placeholder="`Unattributed Value Remaining`"
              class="w-100 form-control position"
              :disabled="true"
            />
          </div>
          <div class="value-div">
            <input
              type="number"
              v-model="unattributedValue"
              :placeholder="`Unattributed Value ${unattributedValue}`"
              class="w-100 form-control value"
              :disabled="true"
            />
          </div>
          <div class="percentage-div">
            <h5>%</h5>
          </div>
        </div>
      </div>
      <div
        class="outer mt-2"
        v-for="(ctrField, index) in ctrFields"
        :key="index"
        v-on:mouseover="controlDelete(index, 'over')"
        v-on:mouseleave="controlDelete(index, 'leave')"
      >
        <div class="input-container">
          <div class="position-div">
            <input
              type="text"
              :placeholder="`Position ${index + 1}`"
              class="w-100 form-control position"
              :disabled="true"
              v-on:change="controlDisable(index)"
            />
          </div>
          <div class="value-div">
            <input
              type="number"
              v-model="ctrField.value"
              :placeholder="`Value ${index + 1}`"
              class="w-100 form-control value"
              :disabled="ctrField.disabled"
              v-on:change="controlDisable(index)"
            />
          </div>
          <div class="percentage-div">
            <h5>%</h5>
          </div>
          <div class="delete-div" v-if="ctrField.deleteIcon">
            <button
              style="max-width: 100%"
              class="btn btn-outline"
              type="button"
              @click="deleteCtrField(index)"
            >
              <span class="fa fa-trash pointer"></span>
            </button>
          </div>
        </div>
        <div class="error" v-if="ctrField.error">
          `Must be lower than position {{ index }}
        </div>
      </div>

      <div style="text-align: center; margin: 10px">
        <button
          style="text-align: center; min-width: 100%"
          class="btn btn-outline-primary"
          type="button"
          @click="addNewCtrFields()"
          :disabled="unattributedValue <= 0"
        >
          Button
        </button>
      </div>
      <br />
    </div>

    <br />
    <button
      style="text-align: center; min-width: 100%"
      class="btn btn-primary"
      type="button"
      :disabled="checkIfApplyIsDisabled()"
    >
      Apply
    </button>

    <br />
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },

  data() {
    return {
      ctrFields: [
        {
          value: 0,
          disabled: false,
          error: false,
          deleteIcon: false,
        },
        {
          value: 0,
          disabled: true,
          error: false,
          deleteIcon: false,
        },
        {
          value: 0,
          disabled: true,
          error: false,
          deleteIcon: false,
        },
        {
          value: 0,
          disabled: true,
          error: false,
          deleteIcon: false,
        },
        {
          value: 0,
          disabled: true,
          error: false,
          deleteIcon: false,
        },
        {
          value: 0,
          disabled: true,
          error: false,
          deleteIcon: false,
        },
      ],
      unattributedValue: this.getSum(),
    };
  },

  methods: {
    addNewCtrFields() {
      let disabled = true;
      if (this.ctrFields[this.ctrFields.length - 1].value !== 0) {
        disabled = false;
      }
      this.ctrFields.push({
        value: 0,
        disabled,
        error: false,
        deleteIcon: false,
      });
    },

    deleteCtrField(index) {
      if (this.ctrFields.length > index + 1) {
        this.ctrFields[index + 1].error = false;
      }
      this.ctrFields.splice(index, 1);
      this.getSum();
    },

    controlDisable(index) {
      if (
        this.ctrFields[index].value === 0 ||
        this.ctrFields[index].value === ""
      ) {
        for (let i = index + 1; i < this.ctrFields.length; i++) {
          this.ctrFields[i].disabled = true;
        }
      } else {
        this.controlValues(index);
        this.controlValues(index + 1);
        if (this.ctrFields.length > index + 1) {
          this.ctrFields[index + 1].disabled = false;
        }
      }
    },

    controlValues(index) {
      if (index !== 0) {
        if (this.ctrFields[index].value >= this.ctrFields[index - 1].value) {
          this.ctrFields[index].error = true;
        } else {
          this.ctrFields[index].error = false;
          this.getSum();
        }
      } else {
        this.getSum();
      }
      this.getSum();
    },

    getSum() {
      let total = 0;
      for (let i = 0; i < this.ctrFields?.length; i++) {
        total = total + this.ctrFields[i].value;
      }
      this.unattributedValue = 100 - total;
      return this.unattributedValue;
    },

    controlDelete(index, param) {
      if (this.ctrFields.length > 6) {
        if (param === "over") {
          this.ctrFields[index].deleteIcon = true;
        }
        if (param === "leave") {
          this.ctrFields[index].deleteIcon = false;
        }
      }
    },

    allocate() {
      let ignore = false;
      for (let i = this.ctrFields.length - 2; i >= 0; i--) {
        if (this.ctrFields[i].value === 0 || this.ctrFields[i].value === "") {
          ignore = true;
        }
      }
      if (this.unattributedValue <= 0) {
        ignore = true;
      }
      if (!ignore) {
        if (
          this.ctrFields[this.ctrFields.length - 1].value === 0 ||
          this.ctrFields[this.ctrFields.length - 1].value === ""
        ) {
          this.ctrFields[this.ctrFields.length - 1].value =
            this.unattributedValue;
        } else {
          this.addNewCtrFields();
          this.ctrFields[this.ctrFields.length - 1].value =
            this.unattributedValue;
        }
        this.controlValues(this.ctrFields.length - 1);
        if (!this.ctrFields[this.ctrFields.length - 1].error) {
          this.getSum();
        }
      }
    },

    checkIfApplyIsDisabled() {
      let disabled = false;
      for (let i = 0; i < this.ctrFields.length; i++) {
        if (this.ctrFields[i].error) {
          disabled = true;
        }
      }

      if (this.unattributedValue !== 0) {
        disabled = true;
      }

      return disabled;
    },
  },
};
</script>

<style scoped>
.pointer {
  cursor: pointer;
  color: red;
}

.main {
  background-color: lightgrey;
}

.input-container {
  display: -ms-flexbox; /* IE10 */
  display: flex;
  width: 100%;
  margin: 10px;
  background-color: white;
  border: 2px solid white;
  border-radius: 10px;
}

.position-div {
  width: 80%;
  margin-right: 1%;
}

.position {
  border: 0;
  outline: 0;
}

.value-div {
  width: 10%;
  margin-right: 1%;
  margin-top: 0.5%;
}

.value {
  border: 0px;
  border-radius: 10px;
  height: 30px;
  align-self: center;
  text-align: center;
  background-color: lightgrey;
}

.percentage-div {
  width: 5%;
  text-align: center;
  margin-top: 1%;
  margin-right: 1%;
}

.delete-div {
  width: 7%;
  border-radius: 250px;
}

input[type="text"]:disabled {
  background: #ffffff;
}

input[type="number"]:disabled {
  background: lightgrey;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type="number"] {
  -moz-appearance: textfield;
}

.outer {
  max-width: 550px;
  margin: 2px;
}

.error {
  font-size: 12px;
  color: red;
  margin-left: 20px;
}
</style>
