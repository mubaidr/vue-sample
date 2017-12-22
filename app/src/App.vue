<template>
  <div id="app">
    <pre>
      {{this.content}}
    </pre>
    <div class="editable" v-html='content' ref='editable' contenteditable="true" @input="update($event)">{{ content }}</div>
  </div>
</template>

<script>
  import _data from './json-data.json'

  export default {
    name: 'app',
    data() {
      return {
        data: _data,
        content: 'This Content is <span style="color: red">Editable</span>...'
      }
    },
    methods: {
      update(event) {
        console.log(event)
      },
      convertJsonToHtml(json) {
        const elem = document.createElement(json.type)
        const childs = json['inner-elements']
        // id
        if (elem.id) {
          elem.id = json.id
        }
        // text
        if (json.text) {
          elem.innerText = json.text
        }
        // styles
        if (json.styles) {
          Object.keys(json.styles).forEach(style => {
            elem.style[style] = json.styles[style]
          })
        }
        // childs
        if (childs && childs.length > 0) {
          childs.forEach(child => {
            elem.innerHTML += this.convertJsonToHtml(child)
          })
        }

        return elem.outerHTML
      }
    },
    mounted() {
      if (Object.keys(this.data).length === 0) return
      this.content = this.convertJsonToHtml(this.data)
    },
    created() {
      console.log(this.data)
    }
  }
</script>

<style>
  body {
    background-color: rgba(0, 0, 0, 0.75);
  }
  .editable {
    color: rgb(255, 255, 255);
    opacity: 1;
    border-radius: 6px;
    transform: rotate(0deg);
    border: 1px solid rgb(70, 144, 247);
    padding: 10px;
    text-align: center;
    letter-spacing: 0px;
    font-size: 32px;
    font-family: Aleo;
    font-weight: 300;
    text-decoration: none;
    box-shadow: rgb(0, 0, 0) 0px 0px 0px 0px;
    text-transform: initial;
    font-style: normal;
    z-index: 2002;
    text-shadow: rgba(0, 0, 0, 0.2) 2px 2px 0px;
    line-height: 1.5em;
  }
</style>
