# 拼豆小店 · 微信小程序

一个面向拼豆（Perler Beads / 拼拼豆豆）门店的微信小程序。基于微信官方**云开发电商模板**改造，集成开源**拼豆图纸生成**能力，支持线上商城 + 线下到店体验预约。

## 功能一览

| 模块 | 说明 | 来源 |
|---|---|---|
| 首页 / 分类 / 商品详情 | 商品浏览 | 云开发电商模板 |
| 购物车 / 下单 / 订单 | 线上购买（微信支付需配置） | 云开发电商模板 |
| 优惠券 / 促销 | 营销能力 | 云开发电商模板 |
| **拼豆图纸** | 传图 → 像素化 → 色号匹配 → 带坐标与用量统计的图纸导出 | 开源移植（见下方版权） |
| **到店体验** | 门店信息 + 体验课程 + 预约表单 | 本项目新增 |

## 项目结构

```
pindou-shop/
├── app.js / app.json          # 小程序入口与配置（云环境 ID 在此配置）
├── config/index.js            # useMock 开关（默认 true，跑本地演示数据）
├── custom-tab-bar/            # 自定义 TabBar（首页/分类/图纸/购物车/我的）
├── pages/
│   ├── home/                  # 首页（含图纸/体验课入口）
│   ├── goods/ cart/ order/    # 商城页面（模板自带）
│   ├── pattern/               # ★ 拼豆图纸：index 选图生成 / result 预览导出
│   └── course/                # ★ 到店体验与课程预约
├── utils/pattern/             # ★ 图纸算法引擎
│   ├── colorData.js           #   205 色 × 5 大色号系统映射（MARD/COCO/漫漫/盼盼/咪小窝）
│   ├── colorUtils.js          #   色板构建、色号查询
│   └── pixelArt.js            #   图片→像素→图纸生成（Canvas 2D）
└── services/ model/ components/  # 模板自带数据层与组件
```

## 快速开始（5 分钟跑起来）

1. **安装微信开发者工具**：<https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html>
2. **导入项目**：打开微信开发者工具 → 导入 → 选择本目录 `pindou-shop`
   - AppID：可先用「测试号」或你自己的小程序 AppID
3. **构建 npm**：菜单栏「工具」→「构建 npm」（模板依赖 TDesign 等组件，已随项目提供）
4. **编译运行**：默认 `useMock: true`，无需后端即可体验全部页面（含拼豆图纸生成）
   - 首页会显示"安装云模板"引导浮层，属正常提示

## 接入真实后端（云开发）

前端使用微信云开发，无需自购服务器与域名备案。

1. **开通云开发**：在微信开发者工具中点击「云开发」按钮开通（个人主体免费额度可用）
2. **安装电商后端模板**（数据模型 + 支付云函数 + 管理后台）：
   - 方式 A：开发者工具 → 云开发控制台 → 「云模板」→ 搜索「云开发电商模板」一键安装
   - 方式 B：访问 <https://tcb.cloud.tencent.com/cloud-template/detail?appName=electronic-business>
   - 模板会创建：商品 SPU/SKU、订单、购物车、优惠券等数据模型与云函数
3. **填写云环境 ID**：修改 `app.js` 中
   ```js
   wx.cloud.init({ env: 'your-env-id' });   // 改为你的环境 ID
   ```
4. **切换真实数据**：修改 `config/index.js`
   ```js
   export const cloudbaseTemplateConfig = { useMock: false };
   ```
5. **到店预约**：云数据库中创建 `appointments` 集合（权限设为"仅创建者可读写"），提交预约即写入

## 微信支付

1. 需要**企业或个体工商户主体**的小程序 + 微信支付商户号
2. 在云开发电商模板后台配置商户号、API 密钥、证书（参考模板文档）
3. 个人主体暂无法开通微信支付，可先用「到店付款」过渡

## 拼豆图纸功能说明

- **图纸 Tab / 首页入口**：上传图片 → 选择尺寸（16~64 格）→ 选择色号系统 → 生成
- 生成结果包含：网格 + 行列坐标 + 每个格子的色号 + 底部色号用量统计（对应"按色号买豆"）
- 图纸可保存到相册、转发分享
- 色号系统：MARD（进口）/ COCO / 漫漫 / 盼盼 / 咪小窝，共 205 色精确映射
- 技术实现：`wx.createOffscreenCanvas` 离屏渲染，全程本地计算，不依赖后端

## 自定义店铺信息

| 位置 | 内容 |
|---|---|
| `app.json` | 小程序标题（当前"拼豆小店"）、TabBar 配色 |
| `pages/course/index/index.js` 顶部 `STORE_INFO` | 门店地址 / 营业时间 / 电话（预约表单展示与拨打） |
| `pages/course/index/index.js` 顶部 `COURSES` | 体验课程列表与价格 |
| 云开发电商模板后台 | 商品、轮播图、分类、优惠券等运营内容 |

## 版权声明

- 商城底座：腾讯云开发电商模板（TencentCloudBase/Cloudbase-Examples，微信官方开源示例）
- 图纸算法与色号数据：移植自开源项目 [liangdabiao/perlerBeadsApplet](https://github.com/liangdabiao/perlerBeadsApplet)（Apache-2.0，作者另行声明后以其为准）及其上游 [noir017/perlerBeadsApplet](https://github.com/noir017/perlerBeadsApplet)
- 移植内容包括：205 色 × 5 色号系统映射、图片像素化与最近色匹配、图纸绘制引擎；已修复源文件中文乱码并移除第三方水印

## 已知限制与后续规划

- 图纸模块为 MVP 版本：未做颜色替换编辑、未做图纸作品收藏（可参考上游项目扩展）
- 商品"色号规格"建议在电商模板后台用 SKU 规格实现（如：色号=规格属性）
- 后续可加：图纸作品广场、色号自动补货提醒、会员积分打通图纸功能
