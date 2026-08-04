---
title: 全国数智医保示范城工作进度管理系统开发总结
date: 2025-09-15 18:01:17
tags:
---

在数智医保城市建设落地场景中，传统线下进度填报、人工汇总统计模式存在数据滞后、统计繁琐、进度不透明、管理低效等核心痛点，无法满足医保示范城项目常态化进度监管、实时数据汇总、多维进度分析的业务需求。基于实际政务管理场景，我独立设计并开发**全国数智医保示范城工作进度管理H5系统**，基于Vue2+Vant+ECharts技术栈，搭建轻量化移动端政务管理平台，实现双模式免密登录、项目进度填报、多维数据可视化统计、无限滚动分页、AES安全加密、跨页面状态通信等全套能力，适配政务人员移动端高效办公、实时监管、数据汇总的核心诉求。

本文将从项目整体介绍、UI设计体系、核心架构与页面模块、核心创新能力、关键技术实现、安全加密机制、接口与路由设计、性能优化、项目亮点与复盘全维度，完整复盘本次政务类H5项目设计与开发实战。

## 一、项目整体介绍

### 1.1 项目基础信息

本项目为**政务类移动端H5管理应用**，面向医保示范城项目管理人员，聚焦项目进度填报、进度查询、数据汇总统计、异常项目监管四大核心场景，支撑医保示范城建设工作的数字化、可视化、常态化管理。

| 项目项   | 详细信息                                                  |
| -------- | --------------------------------------------------------- |
| 项目名称 | 全国数智医保示范城工作进度管理系统                        |
| 技术栈   | Vue 2 + Vant UI + ECharts + Axios + CryptoJS + Moment     |
| 应用类型 | 移动端H5政务管理系统                                      |
| UI设计   | 全程自主独立设计，适配政务视觉规范与移动端交互逻辑        |
| 项目版本 | v1.0.0                                                    |
| 核心场景 | 项目进度填报、多维数据统计、滞后/未填报项目监管、进度追溯 |

### 1.2 技术栈依赖清单

项目采用轻量化、稳定成熟的技术栈，适配政务系统稳定性、安全性、兼容性要求，核心依赖版本如下：

```json
{
  "vue": "2.5.2",
  "vant": "2.12.53",
  "echarts": "5.6.0",
  "axios": "0.21.1",
  "crypto-js": "4.2.0",
  "moment": "2.29.4",
  "vue-router": "3.0.1",
  "vuex": "3.5.1"
}
```

### 1.3 项目核心价值

- **效率升级**：替代传统人工填报、线下汇总模式，实现移动端一键填报、自动统计，大幅降低政务办公成本。
- **数据可视化**：通过多维图表、分类列表直观展示项目进度、滞后情况、未填报状态，实现进度透明化监管。
- **适配政务场景**：支持浙政钉无感知免密登录，适配政务人员日常办公习惯，操作零门槛。
- **安全可控**：全局AES加密敏感数据，区分GET/POST差异化加密规则，保障政务数据传输安全。

## 二、自主UI设计体系

本项目UI完全自主设计，贴合政务系统严谨、简洁、专业的视觉调性，同时适配移动端H5交互规范，搭建标准化、统一化的视觉设计体系，兼顾美观度、专业性与实用性。

### 2.1 全局配色规范

采用政务专属深蓝色为主色调，搭配差异化状态色区分项目进度状态，配色层级清晰、语义明确，符合政务系统视觉认知习惯。

```scss
// 主色调（政务官方蓝）
$primary-blue: #0f40f5;

// 业务状态色
$status-normal: #23a8f2;    // 项目正常
$status-delayed: #ff9800;   // 项目滞后
$status-completed: #52cf6e; // 项目完成

// 警告提示色
$warn-red: #f23726;         // 未填报、高危警告

// 文本层级色
$text-dark: #1d1d1d;        // 核心标题文本
$text-light: #9a9a9a;       // 辅助描述文本
```

### 2.2 核心设计规范

- **卡片规范**：统一10px圆角、4px轻量化阴影、纯白底色，弱化视觉厚重感，适配移动端浏览。
- **布局规范**：采用弹性Flex响应式布局，自适应各类手机屏幕尺寸，无挤压、无错乱。
- **导航规范**：顶部导航栏半透明背景、深色文字，简约大气，适配政务页面调性。
- **按钮规范**：主按钮采用渐变蓝色+圆角设计，主次按钮层级分明。
- **状态标识规范**：左侧颜色边框区分项目状态，红色代表警告、橙色代表滞后，直观高效。
- **徽章规范**：红色圆形徽章标记未填报项目数量，醒目提示待办任务。
- **页面背景**：渐变质感背景，提升页面视觉层次，避免单调刻板。

### 2.3 定制化组件样式

通过深度选择器覆盖Vant默认样式，统一项目UI风格，实现组件视觉定制化：

```scss
// 导航栏定制：透明背景、无边框
::v-deep .van-nav-bar {
  background-color: transparent;
  border-bottom: none;
}

// Tab激活态主题色定制
::v-deep .van-tab--active {
  color: #0f40f5;
}
```

## 三、系统整体架构与页面模块

### 3.1 整体架构设计

项目采用**Vue单页面模块化架构**，基于路由实现页面按需切换，结合keep-alive做页面缓存、sessionStorage做跨页面状态通信、Axios统一接口请求、CryptoJS全局数据加密，形成「视图层-交互层-数据层-安全层」四层闭环架构，结构清晰、职责单一、便于维护迭代。

### 3.2 七大核心页面模块

项目共拆分7个核心业务页面+404异常页面，覆盖项目查看、填报、统计、查询全流程业务场景：

#### 3.2.1 首页（home）

系统入口页面，承载双模式登录、工程卡片展示、待填报数量徽章提醒、快捷导航功能，采用2列网格卡片布局，直观展示所有工程入口，红色徽章醒目提示未填报任务。

#### 3.2.2 项目列表页（projectList）

支持年月筛选、级联选择器筛选，基于无限滚动分页加载项目列表，高效展示海量项目数据，适配移动端长列表浏览场景。

#### 3.2.3 项目详情页（detail）

完整展示项目基础信息、进度时间线历史记录、附件清单、管理员批注信息，实现项目进度全流程追溯。

#### 3.2.4 数据填报页（report）

核心业务填报页面，集成文本进展描述、进度百分比滑块、进度状态单选、多文件上传、数据提交能力，同时配置页面离开保护机制，防止误操作丢失填报数据。

#### 3.2.5 填报汇总页（datatotal）⭐核心页面

系统数据统计核心页面，实现多维数据可视化展示，包含整体进度均值/中位数统计、2×3布局ECharts多维饼图、未填报项目列表、滞后项目进度条列表，双列表独立无限滚动，全方位监管项目填报情况。

#### 3.2.6 进度查看页（progress）

专项项目进度查询页面，聚焦单项目全周期进度数据展示，支持进度明细快速查看。

#### 3.2.7 404异常页面

全局路由异常兜底页面，拦截无效路由跳转，提升系统容错性。

### 3.3 路由缓存机制

基于Vue Router配置页面缓存策略，对高频访问、无需重复刷新的列表统计页面开启keep-alive，提升页面访问速度与操作体验。

```javascript
const routes = [
  { path: '/', redirect: '/index' },
  { path: '/index', name: 'home', meta: { keepAlive: false } },
  { path: '/projectList', name: 'projectList', meta: { keepAlive: true } },
  { path: '/detail', name: 'detail', meta: { keepAlive: false } },
  { path: '/report', name: 'report', meta: { keepAlive: false } },
  { path: '/datatotal', name: 'datatotal', meta: { keepAlive: true } },
  { path: '/progress', name: 'progress', meta: { keepAlive: false } },
  { path: '/404', name: '404', meta: { title: '404' } },
  { path: '*', redirect: '/404' }
]
```

**缓存策略说明**：项目列表、数据汇总页面开启缓存，避免重复请求接口、重复渲染数据；填报、详情、首页等动态页面关闭缓存，保证数据实时性。

## 四、核心创新能力：双登录系统

针对政务系统多场景登录需求，自研**双模式差异化登录体系**，同时支持通用短信验证码登录、浙政钉无感知免密登录，兼顾通用性与政务专属便捷性，适配不同使用场景。

### 4.1 登录方式一：短信验证码登录（通用模式）

采用**三层弹窗递进式校验**机制，手机号→图形验证码→短信验证码层层校验，有效防范恶意刷取、批量登录风险，保障系统登录安全。

```javascript
// 第一层：手机号校验，唤起图形验证码弹窗
async confirmPhone() {
  await this.$refs.phoneForm.validate()
  this.showCaptchaDialog = true
  this.fetchCaptchaImage()
}

// 第二层：获取并处理图形验证码
async fetchCaptchaImage() {
  const res = await getCaptchaImage(this.form.tel)
  if (res.code == 1) {
    let imgStr = res.data?.captImgStr || ''
    // 补全base64前缀，兼容不同后端返回格式
    if (!imgStr.startsWith('data:image')) {
      imgStr = `data:image/png;base64,${imgStr}`
    }
    this.captchaImage = imgStr
  }
}

// 图形验证码校验
async confirmCaptcha() {
  const res = await verifyCaptcha({
    phone: this.form.tel,
    captCode: this.form.code,
    captId: this.captId
  })
  if (res.code == 1) {
    this.showSmsDialog = true
  }
}

// 第三层：短信验证码登录，登录成功缓存用户信息
async confirmSms() {
  const res = await loginValiCode({
    phone: this.form.tel,
    valiCode: this.form.valiCode,
    captCode: this.form.code
  })
  if (res.code == 1) {
    sessionStorage.setItem('userid', JSON.stringify(res.data.userid))
    this.$toast.success('登录成功')
  }
}
```

### 4.2 登录方式二：浙政钉免密登录（政务专属）

深度对接浙政钉政务生态，实现**零操作无感知自动登录**，配套Token自动续期机制，彻底解决政务用户频繁登录、过期重登的痛点，适配政务办公轻量化需求。

```javascript
// App.vue全局自动登录逻辑
created() {
  const token = cookie.get('token')
  const client = cookie.get('client')
  
  if (token && client) {
    // 通过Token自动获取用户信息
    getUserInfoByToken(token, client).then(res => {
      if (res.code === 0 && res.data) {
        let user = res.data
        // 解密后端加密的用户信息
        user.name = decode(user.name)
        user.phone = decode(user.phone)
        sessionStorage.setItem('userid', JSON.stringify(user.name))
      }
    }).catch(() => {
      // Token失效自动续期，无感刷新
      refreshToken(token).then(res => {
        if (res.code === 0) {
          cookie.set('token', res.data)
        }
      })
    })
  }
}
```

### 4.3 双登录模式对比

| 对比维度     | 短信验证码登录         | 浙政钉免密登录             |
| ------------ | ---------------------- | -------------------------- |
| 用户操作成本 | 三层弹窗手动输入校验   | 零操作、完全自动化         |
| 适用场景     | 所有外网普通用户       | 仅限浙政钉政务内网环境     |
| 用户体验     | 标准安全校验流程       | 无感知极速登录             |
| 数据加密     | 登录后本地加密存储     | 后端返回加密数据，前端解密 |
| Token管理    | 短期有效，无需手动续期 | 支持自动续期，长效保活     |

## 五、全局AES安全加密机制

针对政务系统数据安全要求，自研**差异化AES加密体系**，区分GET、POST请求加密规则，适配不同接口传参规范，同时封装安全容错方法，避免空参数报错，全方位保障用户ID等敏感数据传输安全。

### 5.1 加密核心参数

采用标准CBC加密模式、PKCS7填充规则，固定16位密钥与偏移向量，保证加密解密一致性与稳定性：

```javascript
key = "1234567890123456"    // 16字节UTF-8密钥
iv = "0000000000000000"     // 初始化向量
mode = CBC
padding = PKCS7
```

### 5.2 四大核心加密方法

- **encode(str, escapePlus=true)**：标准加密方法，GET请求默认转译加号为%2B，适配URL传参规范。
- **decode(str)**：标准解密方法，自动还原转译字符，完善异常捕获。
- **safeEncode**：安全加密兜底，自动处理null、undefined空参数，避免代码报错。
- **safeDecode**：安全解密兜底，兼容异常加密字符串，提升系统稳定性。

### 5.3 差异化加密使用规范

严格区分请求方式，规避接口传参报错、参数失效问题：

```javascript
// GET请求：开启加号转译，适配URL参数规则
const userId = JSON.parse(sessionStorage.getItem('userid'))
await getDelayProjectList(
  encode(userId),      
  pageSize,
  pageNo
)

// POST请求：关闭加号转译，保留原始加密字符
await addRemark({
  userid: encode(userId, false),  
  remark: message,
  proj_id: id
})
```

## 六、核心业务功能详细实现

### 6.1 无限滚动分页加载

封装通用分页加载逻辑，区分首页初始化与后续增量加载，精准判断数据加载状态，实现无缝无限滚动体验，适配海量项目列表渲染场景。

```javascript
async onLoad() {
  this.loading = true
  try {
    const res = await getProjectList(
      encode(this.userid),
      this.engine_id,
      this.ym,
      this.pageNo,
      this.pageSize
    )
    
    if (res.code !== 1) {
      this.finished = true
      return
    }
    
    const { total, list } = res.data.projList
    
    // 页码为1重置列表，其余增量拼接
    if (this.pageNo === 1) {
      this.progressItems = list
    } else {
      this.progressItems = this.progressItems.concat(list)
    }
    
    // 判断是否加载完毕
    const hasMore = list.length === this.pageSize
    this.finished = !hasMore
    if (hasMore) this.pageNo++
  } catch (error) {
    this.finished = true
  } finally {
    this.loading = false
  }
}
```

### 6.2 ECharts多维图表自适应布局

自研**2×3动态网格布局算法**，自动适配多图表渲染场景，根据图表数量动态计算坐标位置，搭配语义化状态色，实现进度数据多维可视化展示。

```javascript
// 2行3列图表动态布局算法
const colCount = 2
const rowCount = 3
const chartWidth = 40
const chartHeight = 40

for (let i = 0; i < total; i++) {
  const col = i % colCount
  const row = Math.floor(i / colCount)
  // 动态计算图表定位坐标
  const left = col * (chartWidth + 5) + 5 + '%'
  const top = row * (chartHeight + 5) + 5 + '%'
}

// 图表状态配色规则
// 正常：#23a8f2、滞后：#ff9800、完成：#52cf6e
```

### 6.3 项目填报与页面防误退机制

整合表单录入、进度配置、附件上传、数据提交全套填报能力，同时配置路由离开拦截，防止用户误退出页面导致填报数据丢失。

```javascript
// 项目填报提交逻辑
async onSubmit() {
  const userId = JSON.parse(sessionStorage.getItem('userid'))
  const param = {
    userid: encode(userId, false),
    files: this.fileList,
    projReport: {
      proj_id,
      ym,
      process_pct: this.progress,      // 0-100%进度滑块数值
      status: Number(this.radio),      // 0正常/-1滞后/1完成状态
      reporter: encode(userId, false),
      report_time: moment().format('YYYY-MM-DD HH:mm:ss'),
      process_desc: this.message,      // 项目进展描述
      optype: 0                        // 0新增/1修改
    }
  }
  
  const res = await saveProjReportInfo(param)
  if (res.code === 1) {
    this.$toast.success('提交成功')
    setTimeout(() => window.history.back(), 1000)
  }
}

// 页面离开保护，防止误退出丢失数据
beforeRouteLeave(to, from, next) {
  if (this.isSubmitted) {
    next()
  } else {
    this.$dialog.confirm({
      message: '确定要离开当前页面吗？'
    }).then(() => next()).catch(() => next(false))
  }
}
```

## 七、跨页面通信与状态管理

基于sessionStorage实现轻量级全局状态管理，统一存储用户信息、项目参数、滚动位置等核心字段，实现页面跳转数据无缝传递、状态持久化，规避路由传参冗余、刷新数据丢失问题。

### 7.1 全局缓存核心字段

| 缓存字段    | 业务用途         | 示例值         |
| ----------- | ---------------- | -------------- |
| userid      | 当前登录用户标识 | "张三"         |
| engine_id   | 当前选中工程ID   | 123            |
| engine_name | 当前选中工程名称 | "智融共富工程" |
| proj_id     | 当前操作项目ID   | 456            |
| ym          | 当前填报筛选年月 | "202501"       |
| scTop1      | 页面滚动位置缓存 | 数字像素值     |

### 7.2 跨页面传参规范

```javascript
// 跳转页面：缓存参数并路由跳转
sessionStorage.setItem('proj_id', JSON.stringify(proj_id))
sessionStorage.setItem('engine_id', JSON.stringify(engine_id))
this.$router.push({ name: 'detail' })

// 目标页面：解析缓存参数
const proj_id = JSON.parse(sessionStorage.getItem('proj_id'))
const engine_id = JSON.parse(sessionStorage.getItem('engine_id'))
```

## 八、全局API接口清单

项目接口按业务模块拆分，分为业务核心接口、用户登录接口、浙政钉对接接口，结构清晰、便于维护迭代。

### 8.1 核心业务API（home.js）

| 接口名称               | 业务功能             | 请求方式 |
| ---------------------- | -------------------- | -------- |
| getEngineList          | 获取工程列表数据     | GET      |
| getProjectList         | 获取项目分页列表     | GET      |
| getProjectDetl         | 获取项目详情信息     | GET      |
| getProjectSummary      | 获取项目汇总统计数据 | GET      |
| getDelayProjectList    | 获取滞后项目列表     | GET      |
| getUnreportProjectList | 获取未填报项目列表   | GET      |
| saveProjReportInfo     | 提交项目进度填报数据 | POST     |
| addRemark              | 添加项目批注批示     | POST     |
| uploadFile             | 项目附件上传         | POST     |
| getProjectFileList     | 获取项目附件清单     | GET      |

### 8.2 用户登录API（user.js）

包含图形验证码、短信验证码、用户绑定、OpenID登录全套账号体系接口。

### 8.3 浙政钉对接API（ding.js）

包含Token获取用户信息、Token自动续期专属政务对接接口，保障免密登录长效稳定。

## 九、常见问题容错与解决方案

针对开发与运行过程中的高频问题，沉淀标准化容错方案，提升系统稳定性与可维护性。

| 问题现象          | 核心原因                 | 解决方案                        |
| ----------------- | ------------------------ | ------------------------------- |
| 加密结果异常报错  | 传入参数为null/undefined | 统一使用safeEncode安全加密方法  |
| GET接口参数失效   | 未转译加号特殊字符       | GET请求默认使用encode()转译     |
| POST接口参数异常  | 多余转译加号字符         | POST请求使用encode(id, false)   |
| ECharts图表不渲染 | DOM节点未初始化完成      | 通过$nextTick延迟初始化图表     |
| 页面缓存失效      | keepAlive配置错误        | 核对路由meta缓存配置            |
| 分页数据重复叠加  | 页码未正常递增           | 判断hasMore条件，动态更新pageNo |

## 十、性能优化方案

结合移动端H5运行特性，落地多项轻量化性能优化手段，保障系统流畅稳定运行：

- **页面缓存优化**：通过keep-alive缓存高频页面，减少重复接口请求与DOM渲染。
- **路由按需加载**：路由组件异步加载，缩减首屏加载体积，提升首屏速度。
- **DOM时序优化**：使用$nextTick精准控制图表、DOM渲染时序，规避渲染异常。
- **分页增量优化**：采用无限滚动增量加载，避免一次性渲染海量数据导致卡顿。
- **资源加载优化**：开启图片懒加载，减少首屏资源加载压力。
- **样式规范优化**：统一深度选择器使用规范，避免样式冗余、样式污染。

## 十一、项目核心亮点总结

### 11.1 政务双登录创新体系（核心亮点）

首创通用短信登录+浙政钉免密登录双模式，兼顾外网通用性与内网便捷性，搭配Token自动续期机制，解决政务系统登录繁琐、过期失效的行业痛点，适配政务专属办公场景。

### 11.2 差异化AES安全加密机制

针对GET/POST请求特性定制差异化加密规则，配套空参数安全兜底，实现政务敏感数据全程加密传输，兼顾数据安全性与接口兼容性，符合政务数据安全规范。

### 11.3 多维数据可视化自适应

自研2×3动态图表布局算法，结合语义化状态配色，实现项目进度、滞后、完成数据多维可视化展示，数据直观、层级清晰，适配管理人员快速监管需求。

### 11.4 完善的移动端交互体验

集成无限滚动分页、页面离开保护、状态记忆、防误操作机制，同时自主搭建标准化UI设计体系，视觉统一、交互流畅，完全适配移动端政务办公习惯。

### 11.5 高可用轻量化架构

模块化拆分页面与业务逻辑，统一接口、路由、缓存、加密规范，代码结构清晰、容错性强、可维护性高，适配政务系统长期迭代、稳定运行需求。

## 十二、项目总结与复盘

本项目是一套**高安全、高适配、高可用的政务级移动端H5管理系统**，全程自主完成UI设计、交互规范制定、业务逻辑开发、安全机制封装、性能优化全流程工作。基于Vue2轻量化技术栈，针对性解决数智医保示范城项目进度管理、数据汇总、异常监管的核心业务痛点，通过双登录创新机制、差异化AES加密、多维数据可视化、无限滚动优化、页面状态持久化等核心能力，实现政务办公数字化、可视化、高效化升级。

项目完美适配政务内网、外网双场景使用，兼顾**安全性、便捷性、稳定性、美观性**四大核心指标，代码规范、架构清晰、容错完善，完全满足生产环境常态化运行需求，是一套适配政务场景的优质前端实战项目。

**生态对接**：已完整集成浙政钉免密登录生态，适配政务内网办公场景的生产级使用需求。