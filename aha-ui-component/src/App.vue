<template>
  <div id="app" class="row">
    <div class="col-6">
      <div>Button default</div>
      <aha-button
        text="Test button"
        size="sm"
        variant="primary"
        @click="testButton"
        >Test button</aha-button
      >
    </div>
    <div class="col-6">
      <div>Button group</div>
      <aha-button-group size="sm" style="">
        <aha-button variant="primary" @click="testButton">primary</aha-button>
        <aha-button variant="secondary" @click="testButton"
          >secondary</aha-button
        >
        <aha-button variant="success" @click="testButton">success</aha-button>
      </aha-button-group>
    </div>

    <div class="col-6">
      <div>Dropdown with ref</div>
      <aha-dropdown variant="primary">
        <template #button-content>
          Custom <strong>Content</strong> with <em>HTML</em> via Slot
        </template>
        <aha-dropdown-item @click="testButton"> Action </aha-dropdown-item>
        <aha-dropdown-item @click="testButton">
          Another action
        </aha-dropdown-item>
        <aha-dropdown-item @click="testButton">
          Something else here
        </aha-dropdown-item>
      </aha-dropdown>
    </div>
    <div class="col-6">
      <div>Dropdown with text</div>
      <aha-dropdown text="Button text via Prop" variant="primary">
        <aha-dropdown-item @click="testButton"> Action</aha-dropdown-item>
        <aha-dropdown-item @click="testButton">
          Another action
        </aha-dropdown-item>
        <aha-dropdown-item @click="testButton">
          Something else here
        </aha-dropdown-item>
      </aha-dropdown>
    </div>
    <div class="row">
      <h3>Tag Input</h3>
      <div class="col-6">default</div>
      <aha-input>
    </div>
  </div>
</template>

<script>
import AhaDropdown from './components/dropdown/aha-dropdown.vue'
import AhaDropdownItem from './components/dropdown/aha-dropdown-item.vue'
import AhaButton from './components/button/aha-button.vue'
import AhaButtonGroup from './components/button/aha-button-group.vue'
import AhaInput from './components/input/aha-input.vue'
import AhaInputNumber from './components/input/aha-input-number.vue'
import AhaInputMoney from './components/input/aha-input-money.vue'
import AhaInputMobileNumber from './components/input/aha-input-mobile-number.vue'
export default {
  name: 'App',
  components: {
    // HelloWorld,
    AhaButton,
    AhaButtonGroup,
    AhaDropdown,
    AhaDropdownItem,
    AhaInput,
    AhaInputNumber,
    AhaInputMoney,
    AhaInputMobileNumber,
  },
  methods:{
    testButton(){
      console.log("testButton")
    }
  }
}
</script>

<style>
@import "../template/_variables.scss";
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
