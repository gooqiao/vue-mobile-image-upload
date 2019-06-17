<template>
  <div class>
    <transition name="fade">
      <div
        v-if="previewIndex != null"
        style="display:block;"
        class="weui-gallery"
        @click="closePreview"
      >
        <span
          class="weui-gallery__img"
          :style="`background-image:url(${uploadList[previewIndex].src})`"
        ></span>
        <div class="weui-gallery__opr">
          <a href="javascript:" class="weui-gallery__del" @click="removeImg">
            <i class="weui-icon-delete weui-icon_gallery-delete"></i>
          </a>
        </div>
      </div>
    </transition>

    <div class="weui-uploader" ref="uploader">
      <div class="weui-uploader__bd">
        <ul class="weui-uploader__files">
          <li
            @click="preview($event,index)"
            v-for="(item,index) in uploadList"
            :key="index"
            class="weui-uploader__file"
            :style="`background-image:url(${item.src})`"
          >
            <div class="weui-uploader__file-content" v-if="item.content">
              <i v-if="item.content.err" class="weui-icon-warn"></i>
              {{item.content.text}}
            </div>
          </li>
        </ul>
        <div class="weui-uploader__input-box">
          <input
            v-bind="$attrs"
            :key="inputKey"
            @change="fileChange"
            class="weui-uploader__input"
            type="file"
            accept="image/png, image/jpeg, image/gif, image/jpg"
            multiple
          >
        </div>
      </div>
    </div>
  </div>
</template>
<script >
import 'weui'
const URL = window.URL || window.webkitURL || window.mozURL;
export default {
  data() {
    return {
      files: [],
      uploadList: [],
      count: 0,
      previewIndex: null,
      inputKey: 0
    };
  },
  watch: {
    files(newVal) {
      this.$emit("change", newVal);
    }
  },
  methods: {
    fileChange(e) {
      var files = e.target.files;
      console.log(files);
      this.files = this.files.concat(...new Set(files));
      this.setUploadList();
    },
    setUploadList() {
      var files = this.files;
      var uploadList = this.uploadList;
      var list = [];

      var src;
      var start = uploadList.length;
      // 增量更新图片列表
      for (var i = start, len = files.length; i < len; ++i) {
        var file = files[i];

        if (URL) {
          src = URL.createObjectURL(file);
        } else {
          src = e.target.result;
        }
        list.push({ src });
      }
      uploadList = this.uploadList = uploadList.concat(list);

      // 释放后手机无法显示图片
      // if (URL) {
      //   // 释放资源
      //   this.$nextTick(() => {
      //     console.log(uploadList.length);
      //     for (var i = start, len = uploadList.length; i < len; ++i) {
      //       URL.revokeObjectURL(uploadList[i].src);
      //     }
      //   });
      // }
    },
    pushFiles(files) {},
    preview(e, i) {
      this.previewIndex = i;
      // if(URL){
      //   const src = this.uploadList[i].src = URL.createObjectURL(this.files[i])
      //   this.$nextTick(()=>{
      //     URL.revokeObjectURL(src)
      //   })
      // }
    },
    closePreview() {
      this.previewIndex = null;
    },
    resetInput() {
      this.inputKey++;
    },
    removeImg() {
      this.uploadList.splice(this.previewIndex, 1);
      this.files.splice(this.previewIndex, 1);
      this.closePreview();
      this.resetInput();
    }
  },
  created() {
  }
};
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.1s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
