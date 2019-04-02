<template>
    <div class="slot-input" :class="{active : isFocus}" :style="`width:${width}px`" @click="focuInput">
        <span v-if="!isFocus && !input && tagsArr.length === 0" class="placeholder-text">{{placeholder}}</span>
        <div class="group" v-for="(item,index) in tagsArr">
            <!-- tag标签 -->
            <span class="tags">
                <span :title="item" :style="`max-width:${width-32}px`">{{item}}</span>
                <j-icon class="close-btn" size="12" type="closemini" @click="deleteTags(index)"></j-icon>
            </span>
            <!-- 或字dom -->
            <span class="orspan" v-if="index !== tagsArr.length - 1">或</span>
        </div>
        <input class="specil-input" v-model="input" :style="`width:${inputWidth}px;`" @keydown.8="keyback" @keyup="valueChange" @blur="handleBlur" @focus="handleFocus" type="text">
        <div class="computed-long-div">{{input}}</div>
    </div>
</template>

<script>
  export default {
    components: {},
    props: {
        value: {
            default: () => []
        },
        width: {
            default: 400
        },
        placeholder: {
            default: '请输入内容'
        }
    },
    data() {
      return {
          input: '',
          inputWidth: 1,
          isFocus: false,
          tagsArr: this.value
      };
    },
    created() {},
    watch: {
       value(val){
           if(val !== this.tagsArr){
                this.tagsArr = val;
           }
       }  
    },
    methods: {
        valueChange(e) {
            // 计算并设置输入框长度
            this.setInputLong(e);
            // 该判断防止中文输入法问题
            if(e.target.value !== this.input){
                return;
            }
            // 检查英文字符逗号
            let regex = new RegExp(',');
            // 检查中文字符逗号
            let regexTwo = new RegExp('，');
            let bool = regex.test(this.input);
            let bool2 = regexTwo.test(this.input);
            if(bool){
                this.handleData(',');
            }else if(bool2){
                this.handleData('，');
            }
        },
        // 处理数据
        handleData(tag) {
            let temp = this.input.trim().split(tag);
            temp[0] ? this.tagsArr.push(temp[0]) : this.input = '';
            temp[1] ? this.tagsArr.push(temp[1]) : this.input = '';
            this.input = '';
            this.inputWidth = 1;
            // 提交父组件
            this.$emit('input', this.tagsArr);
        },
        // 删除标签
        deleteTags(index) {
            this.tagsArr.splice(index, 1);
        },
        //键盘退回键触发
        keyback(e) {
            // 如果input为空则删除前一个标签
            if(e.target.value === this.input && this.input === ''){
                this.deleteTags(this.tagsArr.length-1);
            }
        },
        // 计算并设置输入框长度
        setInputLong(e) {
            // console.log(e);
            let parent = e.target.parentNode;
            let computedLongDiv = parent.querySelector('.computed-long-div');
            let long = computedLongDiv.clientWidth || 0;
            this.inputWidth = long + 15;
        },
        // 点击使输入框获取焦点
        focuInput(e) {
            if(e.target.className.split(' ').indexOf("slot-input") !== -1){
               e.target.querySelector('input').focus();
            }
            if(e.target.className.split(' ').indexOf("placeholder-text") !== -1) {
                e.target.parentNode.querySelector('input').focus();
            }
        },
        handleBlur() {
            this.isFocus = false;
        },
        handleFocus() {
            if(!this.isFocus){
                this.isFocus = true;
            }
        }
    }
  };
</script>
<style lang="less" scoped>
    .slot-input{
        border: 1px solid #ddd;
        width: 400px;
        min-height: 30px;
        box-sizing: border-box;
        padding-left: 8px;
        outline: none;
        display: flex;
        flex-wrap: wrap;
        align-items: center;

        &.active{
            border-color:#396fff;
        }

        .placeholder-text{
            position: absolute;
            font-size: 12px;
            color: #9B9B9B;
            line-height: 12px;
            font-weight: 400;
        }
        .group {
            display: flex;
            align-items: center;
            .orspan{
                display: block;
                border-radius:2px;
                background-color: #9B9B9B;
                font-size: 12px;
                color: #fff;
                padding: 2px 3px;
                line-height: 14px;
            }
        }
        .tags {
            display: block;
            height: 16px;
            line-height: 16px;
            padding: 1px 5px;
            margin: 3px 5px;
            background-color: #eee;
            color: #333;
            border-radius: 3px;
            span {
                display: inline-block;
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
                font-size: 12px;
            }
        }
        .specil-input {
            /* flex: 1; */
            border: none;
            outline: none;
            width: 5px;
            font-size: 12px;
        }
        .close-btn {
            cursor: pointer;
            color: #6f6b6b;
            float: right;
            line-height: 17px;
        }
        .computed-long-div{
            display: inline-block;
            font-size: 12px;
            height: 0;
            opacity: 0;
        }
    }
</style>
