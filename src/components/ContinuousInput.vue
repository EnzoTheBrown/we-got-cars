<template>
    <div>
      <input type="text" v-model="choice" :placeholder="value" class="continuous">
    </div>
</template>

<script>
export default {
    name: 'ContinuousInput',
    props: ['value', 'data'],
    data () { 
      return { 
        options: [],
        choice: '',
        } 
    },
    methods: {
      selectOption() {
        this.$emit('send-selected-choices', [this.value, [this.choice]])
      }
    },
    watch: {
        'choice': function (newChoice) {
            console.log(newChoice)
            this.selectOption()
        }
    }
}
</script>

<style>
.continuous {
  position: relative;
  width: 260px;
  height: 37px;
  user-select: none;
  font-family: 'Poppins', sans-serif;
  font-size: 16px;
}
</style>