<template>
  <div :style="body_attribute">
    <div id="header" style="overflow:hidden;">      
        <div class="header-logo">NoteBook</div>
        <div class="header-logout" @click="logout">注销</div>
        <div class="header-nickname">{{User.Nickname}}</div>
    </div>
    <div id="main" style="overflow:hidden;">
        <div style="width:1000000px;">
            <div id="catalogue" :style="catalogue_attribute">
            <ul class="catalogue-ul">
                <li :class="{'catalogue-li':true,'catalogue-li-click':catalogue_ul[0].isClick}" @click="turnBlue(catalogue_ul[0].id),newdata()" >{{catalogue_ul[0].text}}</li>
                <li :class="{'catalogue-li':true,'catalogue-li-click':catalogue_ul[1].isClick}" @click="turnBlue(catalogue_ul[1].id)" >{{catalogue_ul[1].text}}</li>
                <ul v-if="catalogue_ul[1].isClick" id="titlelist">
                  <li v-for="title in my_content" :key="title.dataid" @click="showcontent(title.dataid)">{{title.title}}</li>
                </ul>
                <li :class="{'catalogue-li':true,'catalogue-li-click':catalogue_ul[2].isClick}" @click="turnBlue(catalogue_ul[2].id)" >{{catalogue_ul[2].text}}</li>
                <ul v-if="catalogue_ul[2].isClick" id="titlelist">
                  <li v-for="title in trash_content" :key="title.dataid" @click="showcontent(title.dataid)">{{title.title}}</li>
                </ul>
            </ul>
            </div>
            <div id="searchbox" :style="search_attribute">
                <div class="searchbox-search">
                    <div class="searchbox-search-div">
                        <input type="text" id="searchbox-search-input" @blur='search()' placeholder="搜索" />
                    </div>
                </div>
                <div class="searchbox-list">
                    <ul class="searchbox-list-ul">
                        <li class="searchbox-list-li" v-for="content in search_content" :key="content.dataid" @click="showsearchcontent(content.dataid)">{{content.title}}</li>
                    </ul>
                </div>
            </div>
            <div id="content" :style="content_attribute">
                <input type="text" id="content-title" placeholder="标题">
                <div id="content-tag">
                    <ul>
                        <li v-for="tag in showTag" :key="tag.id" class="content-tag-li"><input type="checkbox" name="tag" class="content-tag-input" :value="tag.text" checked>{{tag.text}}</li>
                    </ul>
                    <input type="text" value="" id="content-tag-newtagtxt">
                    <input type="button" value="提交" id="content-tag-newtag-save" @click="newtagsave">
                    <input type="button" value="新建标签" id="content-tag-newtag" @click="newtag">
                    <input type="button" value="删除" id="content-tag-save" @click="delate">
                    <input type="button" value="保存" id="content-tag-save" @click="save">
                    <input type="button" value="恢复" id="content-tag-save" @click="renew" v-if="isdelete">
                </div>
                <!-- <wangEditor v-model="detail" :isClear="isClear" id="content-editor"></wangEditor> -->
                <textarea id="content-editor" rows="3" cols="20"></textarea>
            </div>
        </div>
    </div>
    <input type="text" value="" id="data-id">
  </div>
</template>

<script>
import $ from "jquery";
// import E from "wangeditor";
// import wangEditor from "../components/wangEditor";
import router from '../router';
export default {
  components: {},
  data() {
    return {
      content:[
            // {
            //     data_id:"",
            //     Title:"",
            //     Content:"",
            //     CreateTime:"",
            //     ifdelete:false,
            //     target:[],
            // },
      ],
      my_content:[],
      trash_content:[],
      search_content:[
        // {
        //     data_id:"",
        //     Title:"",
        //     Content:"",
        //     CreateTime:"",
        //     ifdelete:false,
        //     target:[],
        // },    
      ],
      User:{
        Nickname:"",        
        tag_list:[] 
      },
      showTag:[],
      isTag: false,
      isClear: false,
      isdelete:false,
      detail: "",
      body_attribute: {
        width: "",
        height: ""
      },
      catalogue_attribute: {
        width: "",
        height: ""
      },
      search_attribute: {
        width: "",
        height: ""
      },
      content_attribute: {
        width: "",
        height: ""
      },
      catalogue_ul:[
          {id:0,text:"创建新文档",isClick:false},
          {id:1,text:"我的文档",isClick:false},
          {id:2,text:"回收站",isClick:false},
      ],
    };
  },
  methods: {
    ifdelete(){
      var data_id = $("#data-id")[0].value;
      for(var i = 0;i<this.content.length;i++){ 
        if(this.content[i].dataid == data_id){
          if(this.content[i].ifdelete == "0"){
            this.isdelete = true;
          }else if(this.content[i].ifdelete == "1"){
            this.isdelete = false;
          }
        }
      }
    },
    getHeight() {
      this.body_attribute.height = window.innerHeight/50-0.15 + "rem";
      this.body_attribute.width = window.innerWidth/50 -0.15+ "rem";
      this.catalogue_attribute.height = (window.innerHeight/50-2.15) + "rem";
      this.search_attribute.height = (window.innerHeight/50-2.15) + "rem";
      this.content_attribute.height = (window.innerHeight/50-2.15) + "rem";
      this.catalogue_attribute.width = window.innerWidth/50*0.130-0.08 + "rem";
      this.search_attribute.width = window.innerWidth/50*0.104-0.08 + "rem";
      this.content_attribute.width = window.innerWidth/50*0.766-0.08 + "rem";
    },
    turnBlue($index){
      if(this.catalogue_ul[$index].isClick==true){
        this.catalogue_ul[$index].isClick=false;
      }else{
        for(var i = 0;i < 3 ; i++){
          this.catalogue_ul[i].isClick = false;
        }          
        this.catalogue_ul[$index].isClick = true;
      }
      
    },
    id(){
      var data_id = $("#data-id")[0].value;
      for(var i = 0;i<this.content.length;i++){ 
        if(this.content[i].dataid==data_id){
          return true;
        }
      }
      return false;
    },
    del(){
      var data_id = $("#data-id")[0].value;
      for(var i = 0;i<this.trash_content.length;i++){ 
        if(this.trash_content[i].dataid==data_id){
          return true;
        }
      }
      return false;
    },
    showcontent($data_id){
      for(var i = 0;i<this.content.length;i++){ 
        if(this.content[i].dataid==$data_id){
          $("#content-title")[0].value = this.content[i].title;
          $("#content-editor")[0].value = this.content[i].contant;
          $('#data-id')[0].value = $data_id;
          var tag = this.content[i].tag;
          var pos1=0;
          var pos2;
          var tid = 0;
          this.showTag = [];
          while(tag.indexOf('_',pos1)!=-1&&pos1<=tag.length){
            pos2 = tag.indexOf('_',pos1);
            this.showTag.push({id:tid,text:tag.slice(pos1,pos2)});
            pos1=pos2+1;
            tid++;
          }
          break;
        }
      }
      this.ifdelete();
    },
    search(){
      var search_text = $("#searchbox-search-input")[0].value;
      search_text = search_text.toLowerCase();
      this.search_content = [];
      if(search_text==""){
        return;
      }
      for(var i = 0;i<this.my_content.length;i++){ 
        if(this.my_content[i].title.toLowerCase().includes(search_text)){
          this.search_content.push(this.my_content[i]);
        }
      }
    },
    showsearchcontent($id){
      for(var i = 0;i<this.search_content.length;i++){ 
        if(this.search_content[i].dataid==$id){
          $("#content-title")[0].value = this.search_content[i].title;
          $("#content-editor")[0].value = this.search_content[i].contant;
          $('#data-id')[0].value = $id;
          var tag = this.search_content[i].tag;
          var pos1=0;
          var pos2;
          var tid = 0;
          this.showTag = [];
          while(tag.indexOf('_',pos1)!=-1&&pos1<=tag.length){
            pos2 = tag.indexOf('_',pos1);
            this.showTag.push({id:tid,text:tag.slice(pos1,pos2)});
            pos1=pos2+1;
            tid++;
          }
          break;
        }
      }
    },
    newdata(){
      $("#content-title")[0].value = "";
      this.showTag = [];
      this.showTag = this.User.tag_list;
      $("#content-editor")[0].value = "";
      $("#data-id")[0].value = "";
    },
    delate(){
        let dataid = $("#data-id")[0].value;
        const jsondata = {
            data_id:dataid,
        }
        let beforethis = this;
        if(!this.del()){
          $.ajax({
            type: "post",
            url: "http://localhost:8080/NoteBook/delete",
            data: jsondata,
            crossDomain:true, //设置跨域为true
            xhrFields: {
              withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
            },
            success: function(data) {
              beforethis.getUserdata();
            },
            error: function(e) {
                alert("发生未知错误");
            }
          });
        }
        else{
          $.ajax({
            type: "post",
            url: "http://localhost:8080/NoteBook/Tdelete",
            data: jsondata,
            crossDomain:true, //设置跨域为true
            xhrFields: {
              withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
            },
            success: function(data) {
              beforethis.getUserdata();
            },
            error: function(e) {
              alert("发生未知错误");
            }
          });
        }
        
    },
    save(){
      let title = $("#content-title")[0].value;
      let tag_list = this.showTag;
      let content = $("#content-editor")[0].value;
      let tag = "";
      let data_id = $("#data-id")[0].value;
      if(title == ""){
        alert("标题不能为空");
        return;
      }else if(content == ""){
        alert("内容不能为空");
        return;
      }
      for(let i = 0 ;i<tag_list.length;i++){
        tag += tag_list[i].text+'_';
      }
    
      const jsondata = {
        Title:title,
        taglist:tag,
        Content:content,
      }
      let beforethis = this;
      
      if(this.id()){
        const jsondata1 = {
          data_id:data_id,
          Title:title,
          taglist:tag,
          Content:content,
        }
        $.ajax({
          type: "post",
          url: "http://localhost:8080/NoteBook/change",
          data: jsondata1,
          crossDomain:true, //设置跨域为true
          xhrFields: {
            withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
          },
          success: function(data) {
            beforethis.getUserdata();
          },
          error: function(e) {
            alert("发生未知错误");
          }
        });
      }else{
        $.ajax({
          type: "post",
          url: "http://localhost:8080/NoteBook/save",
          data: jsondata,
          crossDomain:true, //设置跨域为true
          xhrFields: {
            withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
          },
          success: function(data) {
            beforethis.getUserdata();
          },
          error: function(e) {
            alert("发生未知错误");
          }
        });
      
      }
      
    },
    renew(){
      let data_id = $("#data-id")[0].value;
      const jsondata = {
        data_id:data_id,
      }
      let beforethis = this;
      $.ajax({
        type: "post",
        url: "http://localhost:8080/NoteBook/renew",
        data: jsondata,
        crossDomain:true, //设置跨域为true
        xhrFields: {
          withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
        },
        success: function(data) {
          beforethis.getUserdata();
        },
        error: function(e) {
            alert("发生未知错误");
        }
      });
    },
    logout(){
      var exp = new Date();
      exp.setDate(exp.getDate()-1);
      document.cookie ="nickname = \"\" ; expires = "+exp.toGMTString();
      this.$router.push('/login');
    },
    newtag(){
      document.getElementById("content-tag-newtagtxt").style.display = "inline";
      document.getElementById("content-tag-newtag-save").style.display = "inline";
    },
    newtagsave(){
      var txt = document.getElementById("content-tag-newtagtxt");
      var btn = document.getElementById("content-tag-newtag-save");
      txt.style.display = "none";
      btn.style.display = "none";
      if(txt.value == ""){
        console.log("输入为空");
        return;
      }
      var tagtxt = {
        id:this.showTag.length+1,
        text:txt.value
      }
      if($("#data-id").value == null){
        //单独做一个接口 完成默认标签的增加
        let defaulttag = this.User.tag_list;
        defaulttag.push(tagtxt);
        let jsontag = "";
        for(var i = 0;i<defaulttag.length;i++){
          jsontag+=defaulttag[i].text+"_";
        };
        const jsondata = {
          tag:jsontag,
        };
        $.ajax({
          type: "post",
          url: "http://localhost:8080/NoteBook/SaveDefaultTag",
          data: jsondata,
          crossDomain: true,
          xhrFields:{
            withCredentials:true
          },
          success:function(){

          },
          error:function(e){
            alert("发生未知错误");
          }
        })
        return;
      }
      let tag_list = this.showTag;
      tag_list.push(tagtxt);
      this.save();
    },
    getUserdata(){
      let cookie1 = document.cookie;
      let pos = cookie1.indexOf("=");
      let nickname = cookie1.slice(pos + 1);
        const jsondata = {
          nickname : nickname
        };
        var nextthis = this;
        $.ajax({
          type: "get",
          url: "http://localhost:8080/NoteBook/UserRes",
          data: jsondata,
          crossDomain:true, //设置跨域为true
          xhrFields: {
            withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
          },
          success: function(data) {
            var x = data.data;
            console.log(x);
            nextthis.content = [];
            nextthis.my_content = [];
            nextthis.trash_content = [];
            for(let i = 0 ;i<x.length;i++){
              nextthis.content.push(x[i]);
              if(x[i].ifdelete == 1){
                nextthis.my_content.push(x[i]);
              }else{
                nextthis.trash_content.push(x[i]);
            }
          }
          },
          error: function(e) {
            alert("发生未知错误");
          }
        });      
      
    }
  }, 
  beforeCreate() {
    let cookie1 = document.cookie;
    let pos = cookie1.indexOf("=");
    let nickname = cookie1.slice(pos + 1);
    const jsondata = {
      nickname : nickname
    };
    var nextthis = this;
    if (document.cookie.length == 0) {
      router.push('login');
    }else{
      $.ajax({
        type: "get",
        url: "http://localhost:8080/NoteBook/UserTag",
        data: jsondata,
        crossDomain:true, //设置跨域为true
        xhrFields: {
          withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
        },
        
        success: function(data) {
          //保存该用户data
          var tag = data.target;
          var pos1=0;
          var pos2;
          var tid = 0;
          while(tag.indexOf('_',pos1)!=-1&&pos1<=tag.length){
            pos2 = tag.indexOf('_',pos1);
            nextthis.User.tag_list.push({id:tid,text:tag.slice(pos1,pos2)});
            pos1=pos2+1;
            tid++;
          }
          nextthis.showTag = nextthis.User.tag_list
        },
        error: function(e) {
          alert("发生未知错误");
        }
      });
    }
  },
  created() {
    window.addEventListener('resize', this.getHeight);
    this.getHeight();
    let cookie1 = document.cookie;
    let pos = cookie1.indexOf("=");
    let nickname = cookie1.slice(pos + 1);
    this.User.Nickname = nickname;
    this.showTag = this.tag_list;
    if (document.cookie.length > 0) {
      const jsondata = {
        nickname : nickname
      };
      var nextthis = this;
      $.ajax({
        type: "get",
        url: "http://localhost:8080/NoteBook/UserRes",
        data: jsondata,
        crossDomain:true, //设置跨域为true
        xhrFields: {
          withCredentials: true //默认情况下，标准的跨域请求是不会发送cookie的
        },
        success: function(data) {
          var x = data.data;
          for(let i = 0 ;i<x.length;i++){
            nextthis.content.push(x[i]);
            if(x[i].ifdelete == 1){
              nextthis.my_content.push(x[i]);
            }else{
              nextthis.trash_content.push(x[i]);
            }
          }
        },
        error: function(e) {
          alert("发生未知错误");
        }
      });      
    }
  },
};
</script>

<style lang="scss" scoped>
#header {
  width: 100%;
  height: 2rem;
  background: #398dee;
  line-height: 2rem;
  color: #101010;
  .header-logo {
    font-size: 0.5rem;
    text-align: center;
    width: 5rem;
    float: left;
  }
  .header-nickname {
    float: right;
    width: 3rem;
  }
  .header-logout {
    float: right;
    width: 3rem;
  }
}
#catalogue {
  width: 5rem;
  float: left;
  border: 1px solid #e0e1e5;
  border-right: none;
  color: #101010;
  text-align: center;
  font-size: 0.5rem;
  .catalogue-li {
    height: 1.7rem;
    line-height: 1.7rem;
  }
  
}
#titlelist{
  font-size: 16px;
}
.catalogue-li-click {
    height: 1.7rem;
    line-height: 1.7rem;
    background: #398DEE;
  }
#searchbox {
  width: 4rem;
  float: left;
  border: 1px solid #e0e1e5;
  text-align: center;
  font-size: 0.5rem;
  .searchbox-search {
    height:1.5rem ;
    border-bottom: 1px solid #e0e1e5;
  }
  .searchbox-search-div {
    width: 87.5%;
    border: 1px solid rgba(148, 157, 166, 0.36);
    border-radius: 0.1rem;
    height: 1rem;
    margin-top: 0.3rem;
    margin-left: 0.23rem;
    #searchbox-search-input {
      width: 96.6%;
      height: 1rem;
      border: none;
      outline: none;
    }
    
  }
  .searchbox-list-ul{
    font-size: 16px;
  }
}
#content {
  float: left;
  #content-title{
      width: 100%;
      height: 10%;
      font-size: 30px;
      border: 1px solid #e0e1e5;
  }
  #content-tag{
      width: 100%;
      height: 5%;
      box-sizing: border-box;
      padding:0.6% 1%;
      .content-tag-li{
          float: left;
      }
      #content-tag-save{
          float: right;
      }
      #content-tag-newtagtxt{
        width: 80px;
        display: none;
      }
      #content-tag-newtag-save{
        display:none;
      }
  }
  #content-editor{
      width: 100%;
      height: 84.2%;
  }
}
#data-id{
  position: absolute;
   top: -9999px;
   left: -9999px;
}
</style>