/**
* @author amberXu
* @date 2019/2/21
* @Description: 职位列表页面
*/
<template>
  <div>
    <nav-header></nav-header>
    <div class="jobs-container">
      <!--头部幻灯片-->
      <!--<section></section>   -->
      <div class="slide-container">
        <el-carousel autoplay indicator-position="outside" :interval="5000" arrow="hover" class="slide-content"
                     :height="imgHeight">
          <el-carousel-item v-for="(item,index) in itemImg" :key="index" name="index" ref="imgHeight">
            <el-row>
              <el-col :span="24">
                <img :src="item.img" class="banner"/>
              </el-col>
            </el-row>

          </el-carousel-item>
        </el-carousel>
      </div>
      <!--头部幻灯片-结束-->
      <!--筛选条件-->
      <div class="filter-container" id="filter" v-bind:class="{'filterBy-show':filterBy}">
        <!--<dl class="filter-salary"></dl>-->
        <!--<dt>薪资待遇：</dt>-->
        <!--<dd><a href="javascript:void(0)" v-bind:class="{'cur':salaryCheck == 'all'}">All</a>-->
        <!--</dd>-->
        <!--<dd v-for="(salary,index) in filterCondition" :key="salary.startSalary">-->
        <!--<a href="javascript:void(0)" @click="setSalaryFilter(index)" v-bind:class="{'cur':salaryCheck==index}">{{salary.startSalary}}-{{salary.endSalary}}</a>-->
        <!--</dd>-->
        <span class="filter-select">已选中:</span>

        <el-select v-model="filterSalary" filterable placeholder="薪资要求" class="filter">
          <el-option
            v-for="(salary,index) in filterCondition.salaryList"
            :key="index"
            :label="salary.salary"
            :value="salary.salary">
          </el-option>
        </el-select>

        <el-select v-model="filterExperience" filterable placeholder="工作经验" class="filter">
          <el-option
            v-for="(experience,index) in filterCondition.experienceList"
            :key="index"
            :value="experience.experience">
          </el-option>
        </el-select>

        <el-select v-model="filterEducation" filterable placeholder="学历要求" class="filter">
          <el-option
            v-for="(education,index) in filterCondition.educationList"
            :key="index"
            :value="education.education">
          </el-option>
        </el-select>

        <el-select v-model="filterFinancing" filterable placeholder="融资情况" class="filter">
          <el-option
            v-for="(financing,index) in filterCondition.financingList"
            :key="index"
            :value="financing.financing">
          </el-option>
        </el-select>

        <el-select v-model="filterCompanyScale" filterable placeholder="公司规模" class="filter">
          <el-option
            v-for="(companyScale,index) in filterCondition.companyScaleList"
            :key="index"
            :value="companyScale.companyScale">
          </el-option>
        </el-select>

        <span><el-button type="primary" plain @click.prevent="searchByFilter">搜索</el-button></span>
        <span><el-button type="primary" plain @click.prevent="removeFilter">清空</el-button></span>

      </div>
      <!--筛选条件-结束-->
      <!--显示职位的卡片-->
      <div>
        <el-row class="jobs-content">
          <el-col :xs="24" :sm="24" :md="12" :lg="12" :xl="6" v-for="item in jobsList" :key="item.jId">
            <div class="grid-content">
              <el-card class="box-card" shadow="hover" body-style="margin:'30px'">
                <div slot="header" class="clearfix">
                  <span class="jName">{{item.jName}}</span>
                  <span class="salary">{{item.salary}}</span>
                  <span class="card-text-margin">
                    <!-- v-bind:href="href" v-on:click="EnterpriseDetailMessage(item.eName)"
                      v-bind:class="{ active: isActive }"-->
                    <router-link
                      :to="{path:'enterprise/introduce',query:{eName:item.eName}}"
                    > {{item.eName}}</router-link>
                  </span>

                  <!---->
                  <el-button style="float: right; padding: 3px 0" type="text"
                             @click="SendingResume(item.jName,item.eName)">投递简历
                  </el-button>

                </div>
                <div class="card-text item">
                  {{item.workAddress.substring(0,2)}}<span class="space"></span>
                  |<span class="space"></span>{{item.education}}<span class="space"></span>
                  |<span class="space"></span>{{item.experienceDuration}}
                  <span class="card-text-margin"><span class="space"></span>
                    <span class="space"></span>{{item.enterpriseMessage.emFinancing}}<span class="space"></span>
                    |<span class="space"></span>{{item.enterpriseMessage.emScaleList}}</span>
                </div>
                <span @click="collectJob(item.jpId, item.jName, item.eName)" style="float: right;cursor: pointer;">
                    <el-icon class="el-icon-star-off" style="color: #ffc107"></el-icon><span style="font-size: 10px;color: #5c5b61;">收藏</span>
                  </span>
              </el-card>
            </div>
          </el-col>

        </el-row>
      </div>
      <!--显示职位的卡片-->
      <footer></footer>
      <!---->
    </div>
  </div>
</template>

<script>

import './../assets/css/base.css'
import NavHeader from '@/components/ZPHeader'
//导入nprogress
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

export default {
  name: 'JobsList',
  data () {
    return {
      jobsList: [],
      //是否收藏
      isCollect: null,
      //幻灯片轮播图
      imgHeight: '480px',
      itemImg: [
        {
          img: '../static/slides/slide-1.jpg'
        },
        {
          img: '../static/slides/slide-2.jpg'
        },
        {
          img: '../static/slides/slide-3.jpg'
        },
        {
          img: '../static/slides/slide-4.jpg'
        }
      ],
      filterCondition:
        {
          salaryList: [
            {
              salary: '1k-3k'
            },
            {
              salary: '3k-5k'
            },
            {
              salary: '5k-8k'
            },
            {
              salary: '8k-10k'
            },
            {
              salary: '>10k'
            }
          ],
          experienceList: [
            {
              experience: '应届生'
            },
            {
              experience: '1年以内'
            },
            {
              experience: '1-3年'
            },
            {
              experience: '3-5年'
            },
            {
              experience: '5-10年'
            },
            {
              experience: '10年以上'
            }
          ],
          educationList: [
            {
              education: '初中及以下'
            },
            {
              education: '中专/中技'
            },
            {
              education: '高中'
            },
            {
              education: '大专'
            },
            {
              education: '本科'
            },
            {
              education: '硕士'
            },
            {
              education: '博士'
            }
          ],
          financingList: [
            {
              financing: '未融资'
            },
            {
              financing: '天使轮'
            },
            {
              financing: 'A轮'
            },
            {
              financing: 'A轮'
            },
            {
              financing: 'C轮'
            },
            {
              financing: 'D轮及以上'
            },
            {
              financing: '已上市'
            },
            {
              financing: '不需要融资'
            }
          ],
          companyScaleList: [
            {
              companyScale: '0-20人'
            },
            {
              companyScale: '20-99人'
            },
            {
              companyScale: '100-499人'
            },
            {
              companyScale: '500-999人'
            },
            {
              companyScale: '1000-9999人'
            },
            {
              companyScale: '10000人以上'
            }
          ]
        },
      salaryCheck: 'all',
      filterBy: false,
      overLayFlag: false,
      //是否激活对应路由
      isActive: '',
      href: '', //对应路由
      // 过滤条件
      filterSalary: '',
      filterExperience: '',
      filterEducation: '',
      responseResult: '',
      filterFinancing: '', // 融资情况
      filterCompanyScale: '', // 公司规模
      condition: [], //  已选中的条件
      category: [
        {
          name: '地区',
          active: false
        },
        {
          name: '工作经验',
          active: false
        }
      ]
    }
  },
  methods: {
    getJobsList () {
      this.$axios.get('getAllJobList').then((result) => {
        this.responseResult = JSON.stringify(result.data)
        if (result.data.code === 200) {
          // 当验证成功后跳转到用户中心
          var res = result.data
          this.jobsList = res.data
        } else {
          this.$message.error(result.data.message + '，请刷新页面')
        }
      })
        .catch(function (error) {
          console.log(error)
          alert(error)
        })
    },
    //收藏
    collectJob (jpId, jName, eName) {
      var loginCandidatePhone = localStorage.getItem('loginCandidatePhone')
          this.$confirm('确认收藏该公司吗？', '提示', {}).then(() => {
            NProgress.start()
            this.$axios.post('/candidate/addCollection', {
              cphone: loginCandidatePhone,
              jpId: jpId,
              jName: jName,
              eName: eName,
              collectTime: new Date().toLocaleDateString(),
            }).then((res) => {
              this.editLoading = false
              NProgress.done()

              if(res.data.code === 200){
                this.$message({
                  message: '收藏成功，请到您的收藏夹进行查看',
                  type: 'success'
                })
              }else{
                this.$message.error(res.data.message)
              }
            }).catch((error) =>{
              alert(error)
            })
          })
    },
    SendingResume (jName, eName) {
      console.log(jName, eName)
      var loginCandidatePhone = localStorage.getItem('loginCandidatePhone')
      var loginCandidate = JSON.parse(localStorage.getItem('loginCandidate'))
      if (loginCandidatePhone === null || loginCandidatePhone === '') {
        this.$message.error('请先登录您的个人账号')
      } else {
        console.log(loginCandidate)
        this.$confirm('确认将您的简历投递到该公司吗？', '提示', {}).then(() => {
          NProgress.start()

          this.$axios.post('candidate/sendingResume', {

            jName: jName,
            eName: eName,
            phone: loginCandidatePhone,
            date: new Date().toLocaleDateString(),
            status: '已投递'
          })
            .then((result) => {
              NProgress.done()

              this.responseResult = JSON.stringify(result.data)
              this.loading = false
              // console.log(this.responseResult)
              if (result.data.code === 200) {
                this.$message({
                  message: '简历投递成功',
                  type: 'success'
                })
              } else {
                this.$message.error(result.data.message + '请重新投递')
              }

            })

        })
      }

    },
    showFilterPop () {
      this.filterBy = true
      this.overLayFlag = true
    },
    closePop () {
      this.filterBy = false
      this.overLayFlag = false
    },
    setSalaryFilter (index) {
      this.salaryCheck = index
      this.closePop()
    },
    //通过筛选条件搜索岗位,从后端获取对应数据
    searchByFilter () {
      this.$axios.get('getJobListByFilterCondition', {
        params: {
          filterSalary: this.filterSalary,
          filterExperience: this.filterExperience,
          filterEducation: this.filterEducation,
          filterFinancing: this.filterFinancing,// 融资情况
          filterCompanyScale: this.filterCompanyScale // 公司规模
        }
      }).then(result => {
          this.responseResult = JSON.stringify(result.data)
          this.loading = false
          // console.log( this.responseResult)
          if (result.data.code === 200) {
            // 筛选后返回的数据
            this.jobsList = result.data.data
            // console.log(this.jobsList)
          } else {
            this.$message.error(result.data.message + '请重新选择筛选条件')
          }
        }
      ).catch(function (error) {
        console.log(error)
        // this.loading = false
      })
    },
    // 跳转到详细的公司信息页面 Detail
    EnterpriseDetailMessage (emname) {
      alert(emname)
      this.$route.params.eName = emname
      // 跳转到对应的公司详情页面
      this.$router.push({
        path: '/enterprise/jobmessage',
        params: {
          enname: emname
        }
      })
    },
    // 全选
    allIn (index) {

    },
    //  清空
    removeFilter () {
      this.filterSalary = null
      this.filterExperience = null
      this.filterEducation = null
      this.filterFinancing = null
      this.filterCompanyScale = null
    }
  },
  components: {
    NavHeader
  },
  mounted () {
    this.getJobsList()
    this.jobsList.collectStar = true
  }
}
</script>
<style scoped>
  .jobs-container {
    min-width: 1000px;
    margin: 20px 100px;
  }

  a {
    text-decoration: none;
    color: #3c3c3c;
  }

  /*幻灯片的样式*/
  .el-carousel__item {
    display: block;
  }

  .slide-content img {
    height-width: 100%;
    width: 80%;
  }

  .slide-container {
    margin: 20px 30px;
    text-align: center;
  }

  /*筛选条件的样式*/
  .filter-container {
    background: #fff;
    margin: 10px 0;
    padding: 20px;
  }

  .filter-container .filter-select {
    margin: 10px;
  }

  /*多选框*/
  .filter {
    width: 8rem;
  }

  /*职位列表*/
  /*职位名称*/
  .job-name {
    float: left;
  }

  /*筛选条件之间的margin等格式的调整*/
  .el-select {
    margin-right: 10px;
  }

  /*筛选条件的content中的按钮的格式*/
  .el-button--primary.is-plain {
    margin-right: 5px;
  }

  /*职位信息和公司信息之间的margin*/
  .card-text-margin {
    margin-left: 2.5rem;
  }

  .card-text {
    font-size: 10px;
    color: #b7b3c3;
  }

  .salary {
    color: #ff2126;
  }

  .space {
    margin: 0 0.2rem;
  }
</style>
