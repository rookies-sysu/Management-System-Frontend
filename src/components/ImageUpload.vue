<template>

    <div class="upload">
      <div class="upload_warp">
        <div class="upload_warp_left" @click="fileClick">
          <img src="../assets/upload.png">
        </div>
        <img :src="image" class="oriImage" v-if="imgNotChange">
        <input @change="fileChange($event)" type="file" id="upload_file" multiple style="display: none"/>
        <div class="upload_warp_img" v-show="imgList.length!=0">
          <div class="upload_warp_img_div" v-for="(index, item) in imgList">
            <div class="upload_warp_img_div_top">
              <div class="upload_warp_img_div_text">
                {{item.file.name}}
              </div>
              <img src="../assets/del.png" class="upload_warp_img_div_del" @click="fileDel(index)">
            </div>
            <img :src="item.file.src">
          </div>
        </div>
      </div>
      <div class="upload_warp_text">
        {{bytesToSize(this.size)}}
      </div>

    </div>

</template>

<script>
  export default {
    name: 'image-upload',
    props: {
      image: {
        type: String,
        required: true
      }
    },
    data () {
      return {
        imgList: [],
        size: 0,
        imgNotChange: true
      }
    },
    methods: {
      fileClick () {
        document.getElementById('upload_file').click()
      },
      fileChange (el) {
        if (!el.target.files[0].size) return
        this.fileList(el.target)
        el.target.value = ''
      },
      fileList (fileList) {
        let files = fileList.files
        for (let i = 0; i < files.length; i++) {
          if (files[i].type !== '') {
            this.fileAdd(files[i])
          } else {
            this.folders(fileList.items[i])
          }
        }
      },
      folders (files) {
        let _this = this
        if (files.kind) {
          files = files.webkitGetAsEntry()
        }
        files.createReader().readEntries(function (file) {
          for (let i = 0; i < file.length; i++) {
            if (file[i].isFile) {
              _this.foldersAdd(file[i])
            } else {
              _this.folders(file[i])
            }
          }
        })
      },
      foldersAdd (entry) {
        let _this = this
        entry.file(function (file) {
          _this.fileAdd(file)
        })
      },
      fileAdd (file) {
        this.imgNotChange = false
        if (this.limit !== undefined) this.limit--
        if (this.limit !== undefined && this.limit < 0) return
        this.size = this.size + file.size
        if (file.type.indexOf('image') === -1) {
          file.src = 'wenjian.png'
          this.imgList.push({
            file
          })
        } else {
          let reader = new window.FileReader()
          let image = document.createElement('img')
          let _this = this
          reader.readAsDataURL(file)
          reader.onload = function () {
            file.src = this.result
            image.onload = function () {
              let width = image.width
              let height = image.height
              file.width = width
              file.height = height
              _this.imgList.push({
                file
              })
            }
            image.src = file.src
          }
        }
      },
      fileDel (index) {
        this.size = this.size - this.imgList[index].file.size
        this.imgList.splice(index, 1)
        if (this.imgList.length === 0) {
          this.imgNotChange = true
        }
        if (this.limit !== undefined) this.limit = this.imgList.length
      },
      bytesToSize (bytes) {
        if (bytes === 0) return '0 B'
        let k = 1024
        let sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB']
        let i = Math.floor(Math.log(bytes) / Math.log(k))
        return (bytes / Math.pow(k, i)).toPrecision(3) + ' ' + sizes[i]
      }
    }
  }
</script>
<style scoped>
  .oriImage {
    margin-left: 30px;
    vertical-align: middle;
    height: 120px;
    height: 120px;
  }
  .upload_warp_img_div_del {
    position: absolute;
    top: 6px;
    width: 16px;
    right: 4px;
  }

  .upload_warp_img_div_top {
    position: absolute;
    top: 0;
    width: 100%;
    height: 30px;
    background-color: rgba(0, 0, 0, 0.4);
    line-height: 30px;
    text-align: left;
    color: #fff;
    font-size: 12px;
    text-indent: 4px;
  }

  .upload_warp_img_div_text {
    white-space: nowrap;
    width: 80%;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .upload_warp_img_div img {
    max-width: 100%;
    max-height: 100%;
    vertical-align: middle;
  }

  .upload_warp_img_div {
    position: relative;
    height: 100px;
    width: 120px;
    border: 1px solid #ccc;
    margin: 0px 30px 10px 30px;
    float: left;
    line-height: 100px;
    display: table-cell;
    text-align: center;
    background-color: #eee;
    cursor: pointer;
  }

  .upload_warp_img {
    border-top: 1px solid #D2D2D2;
    padding: 14px 0 0 14px;
    overflow: hidden
  }

  .upload_warp_text {
    text-align: left;
    margin-bottom: 10px;
    padding-top: 10px;
    text-indent: 14px;
    border-top: 1px solid #ccc;
    font-size: 14px;
  }

  .upload_warp_left img {
    margin-top: 32px;
    margin-left: 40px;
  }

  .upload_warp_left {
    float: left;
    width: 40%;
    height: 100%;
    border: 1px dashed #999;
    border-radius: 4px;
    cursor: pointer;
  }

  .upload_warp {
    margin: 14px;
    height: 130px;
  }

  .upload {
    /*margin-top: 20px;*/
    margin-left: 3px;
    margin-top: 6px;
    border: 1px solid #ccc;
    background-color: #fff;
    width: 400px;
    height: 200px;
    box-shadow: 0px 1px 0px #ccc;
    border-radius: 4px;
  }

</style>
