<template>
  <div style="margin-top: -30px">
    <el-row :gutter="30">
      <el-col :xs="24" :sm="12" :md="12" :lg="12" style="margin-top: 30px">
        <div class="panel">
          <div class="panel-content">
            <div class="header">
              <div class="title">标签列表</div>
            </div>
            <ul class="meta-list">
              <li v-for="tag in tags">
                <span class="meta" @click="clickTag(tag.id,tag.name)">{{tag.name}}</span>
                <span style="float: right;clear: both">
                <span class="meta-count">{{tag.count}}</span>
                <el-button type="danger" size="small" @click="deleteTagHandle(tag.name)">删除</el-button>
              </span>
              </li>
            </ul>
            <el-input placeholder="请输入标签名称" class="meta-input" v-model.trim="tagName"></el-input>
            <el-button type="success" size="small" style="float: right;clear: both" @click="saveOrUpdateTag">保存标签</el-button>
          </div>
        </div>
      </el-col>
      <el-col :xs="24" :sm="12" :md="12" :lg="12" style="margin-top: 30px">
        <div class="panel">
          <div class="panel-content">
            <div class="header">
              <div class="title">分类列表</div>
            </div>
            <ul class="meta-list">
              <li v-for="category in categories">
                <span class="meta" @click="clickCategory(category.id,category.name)">{{category.name}}</span>
                <span style="float: right;clear: both">
                <span class="meta-count">{{category.count}}</span>
                <el-button type="danger" size="small" @click="deleteCategoryHandle(category.name)">删除</el-button>
              </span>
              </li>
            </ul>
            <el-input placeholder="请输入分类名称" class="meta-input" v-model.trim="categoryName"></el-input>
            <el-button type="success" size="small" style="float: right;clear: both" @click="saveOrUpdateCategory">保存分类</el-button>
          </div>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    data () {
      return {
        tags: [],
        categories: [],
        tagId: '',
        tagName: '',
        categoryId: '',
        categoryName: ''
      }
    },
    methods: {
      getTags () {
        this.$api.auth.getAllTags().then(data => {
          if (data.success) {
            for (let key in data.data) {
              this.tags.push(data.data[key])
            }
          } else {
            this.$message({
              message: '获取tag失败,' + data.msg,
              type: 'error'
            })
          }
        })
      },
      getCategories () {
        this.$api.auth.getAllCategories().then(data => {
          if (data.success) {
            for (let key in data.data) {
              this.categories.push(data.data[key])
            }
          } else {
            this.$message({
              message: '获取category失败,' + data.msg,
              type: 'error'
            })
          }
        })
      },
      clickTag (tagId, tagName) {
        this.tagId = tagId
        this.tagName = tagName
      },
      clickCategory (categoryId, categoryName) {
        this.categoryId = categoryId
        this.categoryName = categoryName
      },
      deleteTagHandle (tagName) {
        this.$confirm('确定删除该标签?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'error'
        }).then(() => {
          this.$api.auth.delete(tagName).then(data => {
            if (data.success) {
              this.refreshTags()
              this.$message({
                type: 'success',
                message: '删除成功!'
              })
            } else {
              this.$message({
                message: '删除tag失败,' + data.msg,
                type: 'error'
              })
            }
          })
        }).catch(() => {
        })
      },
      deleteCategoryHandle (categoryName) {
        this.$confirm('确定删除该分类?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'error'
        }).then(() => {
          this.$api.auth.deleteCategory(categoryName).then(data => {
            if (data.success) {
              this.refreshCategories()
              this.$message({
                type: 'success',
                message: '删除成功!'
              })
            } else {
              this.$message({
                message: '删除category失败,' + data.msg,
                type: 'error'
              })
            }
          })
        }).catch(() => {
        })
      },
      saveOrUpdateTag () {
        if (this.tagName === null || this.tagName === '') {
          this.$message({
            message: '标签名称不能为空',
            type: 'error'
          })
          return
        }
        if (this.tagId !== null && this.tagId !== '') {
          this.$api.auth.updateTag(this.tagId, this.tagName).then(data => {
            if (data.success) {
              this.refreshTags()
              this.$message({
                message: '更新tag成功!',
                type: 'success'
              })
            } else {
              this.$message({
                message: '更新tag失败,' + data.msg,
                type: 'error'
              })
            }
          })
        } else {
          this.$api.auth.saveTag(this.tagName).then(data => {
            if (data.success) {
              this.refreshTags()
              this.$message({
                message: '新建tag成功!',
                type: 'success'
              })
            } else {
              this.$message({
                message: '新建tag失败,' + data.msg,
                type: 'error'
              })
            }
          })
        }
      },
      saveOrUpdateCategory () {
        if (this.categoryName === null || this.categoryName === '') {
          this.$message({
            message: '分类名称不能为空',
            type: 'error'
          })
          return
        }
        if (this.categoryId !== null && this.categoryId !== '') {
          this.$api.auth.updateCategory(this.categoryId, this.categoryName).then(data => {
            if (data.success) {
              this.refreshCategories()
              this.$message({
                message: '更新category成功!',
                type: 'success'
              })
            } else {
              this.$message({
                message: '更新category失败,' + data.msg,
                type: 'error'
              })
            }
          })
        } else {
          this.$api.auth.saveCategory(this.categoryName).then(data => {
            if (data.success) {
              this.refreshCategories()
              this.$message({
                message: '新建category成功!',
                type: 'success'
              })
            } else {
              this.$message({
                message: '新建category失败,' + data.msg,
                type: 'error'
              })
            }
          })
        }
      },
      refreshTags () {
        this.tags = []
        this.getTags()
      },
      refreshCategories () {
        this.categories = []
        this.getCategories()
      }
    },
    mounted () {
      this.getTags()
      this.getCategories()
    }
  }
</script>

<style scoped>

  .meta-list {
    margin: 0 0 30px 0;
    padding: 0;
    list-style: none;
  }

  .meta-list li {
    line-height: 2.4rem;
  }

  .meta-list .meta {
    box-shadow: 0 0 3px rgba(14, 14, 14, 0.3);
    margin: .4rem;
    max-width: 350px;
    padding: .4rem .5rem;
    white-space: nowrap;
    cursor: pointer;
    font-size: 14px;
    font-weight: 600;
    color: #333;
    border: 1px solid #ffd740;
    background-color: #ffd740;
  }

  .meta-list .meta:hover {
    box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.16), 0 2px 10px 0 rgba(0, 0, 0, 0.12);
    transition: all .2s;
  }

  .meta-list .meta:active {
    box-shadow: inset 0 3px 5px rgba(0, 0, 0, .125);
  }

  .meta-list .meta-count {
    display: inline-block;
    min-width: 10px;
    line-height: 12px;
    padding: 4px 7px;
    margin-right: 20px;
    font-size: 11px;
    font-weight: 700;
    color: #fff;
    text-align: center;
    white-space: nowrap;
    vertical-align: baseline;
    background-color: #F36A5A;
    border-radius: 10px;
  }

  .meta-input {
    width: 200px;
    margin-left: 5px;
    display: inline-block;
  }


</style>
