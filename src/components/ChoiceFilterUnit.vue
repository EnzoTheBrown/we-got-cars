<template>
    <div :key="choice._id" class="custom-option" 
         :class="{ 'selected': selected, 'open': isOpen }"
         @click.stop="selectOption(choice)">
        {{ capitalizeWords(displayName(choice)) }} ({{ choice.count }})
    </div>
</template>

<script>
export default {
    name: 'ChoiceFilterUnit',
    props: ['choice', 'selected'],
    data () { 
      return { 
        options: [],
        isOpen: false,
        } 
    },
    methods: {
      capitalizeWords(str) {
          if (str === null) {
              return ''
          }
          if (str === undefined) {
              return ''
          }
          return str.replace(/\b(\w)/g, s => s.toUpperCase())
      },
      displayName(obj) {
        if (obj.name === undefined) {
            return obj._id
        }
        return obj.name
      },
      selectOption(choice) {
        this.$emit('select-option', choice)
      }

    },
}
</script>

<style scoped>
    .custom-option {
      line-height: 50px;
      border: 1px solid #888888;
      text-align: center;
      text-overflow: ellipsis; 
      
    }
    .custom-option.selected {
    background-color: #888888;
    }

    .custom-option:hover {
    background-color: #888888;
    }
</style>