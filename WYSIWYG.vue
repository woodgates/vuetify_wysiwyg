<template>
  <div>
    <v-input 
    class="wysiwyg"
    ref="vinput"
    :flat="flat"
    :value="input.html"
    :rules="[v => (!!v && this.input.text.length > minLenght) || 'Debe tener al menos '+minLenght+' letras']"
    :name="name"
    :id="name"
    >
    <v-flex xs12>
      <v-card>
        <v-card-text
          class="grey lighten-4"
          :flat="flat" 
          ref="editbox"
          contenteditable="true"
          @input="textInput($event.target)"
          ></v-card-text>
        <v-card-actions v-if="textLength || isEdit">
          <div>
            <v-btn 
              small 
              @click="formatText('fontSize','4')">
              <v-icon>title</v-icon>
            </v-btn>
            <v-btn 
              small 
              @click="formatText('underline')">
              <v-icon>format_underline</v-icon>
            </v-btn>
            <v-btn 
              small 
              @click="formatText('bold')">
              <v-icon>format_bold</v-icon>
            </v-btn>
            <v-btn 
              small 
              @click="formatText('italic')">
              <v-icon>format_italic</v-icon>
            </v-btn>
            <v-btn 
              small 
              @click="formatQuote">
              <v-icon>format_quote</v-icon>
            </v-btn>
            <v-btn 
              small 
              @click="formatText('insertUnorderedList')">
              <v-icon>format_list_bulleted</v-icon>
            </v-btn>
            <v-btn
            small
            class="indigo darken-2"
            @click="formatText('foreColor','#303F9F')"></v-btn>
            <v-btn
            small
            class="red darken-2"
            @click="formatText('foreColor','#D32F2F')"></v-btn>
            <v-btn
            small
            class="teal darken-2"
            @click="formatText('foreColor','#00796B')"></v-btn>
          </div>
          <v-spacer></v-spacer>
          <div class="savecancel"
          v-if="isEdit"
          >
          <v-btn small @click="save" :disabled="hasError" :loading="loading"><v-icon>save</v-icon></v-btn>
          <v-btn small @click="cancel" :disabled="loading"><v-icon>cancel</v-icon></v-btn>
          </div>
        </v-card-actions>
      </v-card>
        </v-flex>
    </v-input>
  </div>
</template>

<script>
export default {
  props:{
    name:{
      required:true,
      type:String
    },
    value:null,
    minLenght:{
      type:Number,
      default:10
    },
    flat:{
      type:Boolean,
      default:false
    },
    isEdit:{
      type:Boolean,
      default:false
    }
  },
  data(){
    return {
      input:{
        html:null,
        text:null
      },
      vinput:null,
      loading:false,
      oldValue:''
    }
  },
  methods: {
    textInput(target){
      this.input.html = target.innerHTML;
      this.input.text = target.innerText;
      this.$emit('input',this.input.html)
    },
    save(){
      this.loading=true
      this.$emit('save',{
        field:this.name,
        value:this.value
      })
    },
    cancel(){
      this.$emit('cancel',this.oldValue)
    },
    formatText(command,value){
      document.execCommand(command, false, value);
    },
    formatQuote(){
      document.execCommand("formatBlock", false, '<blockquote>');
      let selection = window.getSelection().focusNode.parentNode;
      selection.classList.toggle("blockquote")
      this.textInput(this.$refs.editbox)
    },
  },
  computed:{
    textLength(){
      return this.input.text ? this.input.text.length :0
    },
    hasError(){
     return this.vinput ? this.vinput.hasError : null
    },
  },
  watch:{
    value(value){
      if(!value){
        this.$refs.editbox.textContent=""
        this.input.text=null
        this.input.html=null
      }
    }
  },
  mounted(){
    
    if(this.isEdit){
      this.oldValue = this.value
      this.$refs.editbox.innerHTML=this.value;
      this.$refs.editbox.focus()
      this.vinput = this.$refs.vinput
    }
    document.execCommand("defaultParagraphSeparator", false, "div");

  }
}
</script>

<style scoped>
.wysiwyg{
  width:100%
}
.wysiwyg .v-btn{
  min-width:30px
}

</style>
