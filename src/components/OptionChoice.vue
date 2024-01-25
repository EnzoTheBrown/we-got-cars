<template>
  <div class="custom-select-wrapper" @click="toggleSelect" ref="myComponent">
    <div class="custom-select">
      <div class="custom-select__trigger"><span>{{ formatName() }}</span>
        <div class="arrow" :class="{ 'open': isOpen }"></div>
      </div>
      <div class="custom-options" v-show="isOpen">
        <div v-for="choice in filterChoices()" :key="choice._id">
          <ChoiceFilterUnit v-on:select-option="addOption(choice)" :choice="choice" :selected="isSelected(choice._id)"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import ChoiceFilterUnit from './ChoiceFilterUnit.vue'

export default {
    name: 'OptionChoice',
    props: ['category', 'data'],
    components: {
      ChoiceFilterUnit
    },
    data () { 
      return { 
        options: [],
        selected: [],
        isOpen: false
        } 
    },
    methods: {
      formatName() {
        if (this.selected.length > 0) {
          return this.category + ': ' + this.selected.join(', ')
        }
        else {
          return this.category
        }
      },
      toggleSelect() {
        this.isOpen = !this.isOpen;
      },
      isSelected(id) {
        return this.selected.includes(id)
      },
      filterChoices() {
        if (this.data[this.category] === undefined) {
          return []
        }
        return this.data[this.category].filter(choice => choice._id !== '')
      },
      addOption(choice) {
        if (this.selected.includes(choice._id)) {
          this.selected = this.selected.filter(item => item !== choice._id)
        }
        else {
            this.selected.push(choice._id)
        }
        this.$emit('send-selected-choices', [this.category, this.selected])
      },
    handleClickOutside(event) {
        if (this.$refs.myComponent && !this.$refs.myComponent.contains(event.target)) {
    this.isOpen = false;
  }
},
    },
  mounted() {
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.handleClickOutside);
  },
}
</script>

<style scoped>
.custom-select-wrapper {
  position: relative;
  width: 260px;
  user-select: none;
}

.custom-select {
  position: relative;
  display: flex;
  flex-direction: column;
}

.custom-select__trigger {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 22px;
  font-size: 16px;
  color: #3f3f3f;
  height: 40px;
  line-height: 40px;
  background: #ffffff;
  border: 1px solid #d1d1d1;
  cursor: pointer;
}

.custom-options {
  position: absolute;
  display: block;
  top: 100%;
  left: 0;
  right: 0;
  border: 1px solid #d1d1d1;
  background-color: #ffffff;
  transition: all 0.5s;
  overflow: hidden;
  z-index: 2;
}

.custom-select-wrapper .open .custom-options {
  max-height: 300px; /* Set a maximum height */
  overflow-y: auto; /* Enable vertical scrolling */
}


.custom-select.open .custom-options {
  opacity: 1;
  visibility: visible;
  pointer-events: all;
}


.arrow {
  position: relative;
  height: 10px;
  width: 10px;
}
</style>
