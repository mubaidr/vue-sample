<template>
  <div id="app">
    <div class="editable" v-html='content' ref='editable' contenteditable="true" @input="update">{{ content }}</div>
    <pre>{{json}}</pre>
  </div>
</template>

<script>
  import _data from './json-data.json'

  export default {
    name: 'app',
    data() {
      return {
        data: _data,
        content: 'This Content is <span style="color: red">Editable</span>...',
        json: _data,
        timer: null
      }
    },
    mounted() {
      if (Object.keys(this.data).length === 0) return
      this.content = this.convertJsonToHtml(this.data)
    },
    created() {
      // console.log(this.data)
    },
    methods: {
      update() {
        window.clearTimeout(this.timer)
        this.timer = window.setTimeout(() => {
          console.log(this)
          this.json = this.convertHtmlToJson(this.$refs.editable)
        }, 500)
      },
      convertHtmlToJson(elem) {
        const childs = []
        if (elem.childNodes) {
          elem.childNodes.forEach(node => {
            childs.push(this.convertHtmlToJson(node))
          })
        }
        return {
          id: elem.id,
          styles: elem.style,
          text: elem.innerText,
          'inner-elements': childs
        }
      },
      convertJsonToHtml(json) {
        const elem = document.createElement(
          json.type === 'text' ? 'span' : json.type
        )
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
            // skip position absolute
            if (style !== 'position') {
              elem.style[style] = json.styles[style]
            }
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
    }
  }
</script>

<style>
  body {
    color: #eee;
    background-color: rgba(0, 0, 0, 0.75);
  }

  .editable {
    border-radius: 6px;
    border: 1px solid rgb(70, 144, 247);
    box-shadow: rgb(0, 0, 0) 0px 0px 0px 0px;
    color: rgb(255, 255, 255);
    font-family: Aleo;
    font-size: 32px;
    font-style: normal;
    font-weight: 300;
    letter-spacing: 0px;
    line-height: 1.5em;
    margin: 0 auto;
    opacity: 1;
    padding: 10px;
    text-align: center;
    text-decoration: none;
    text-shadow: rgba(0, 0, 0, 0.2) 2px 2px 0px;
    text-transform: initial;
    transform: rotate(0deg);
    width: 50%;
    z-index: 2002;
  }
</style>
