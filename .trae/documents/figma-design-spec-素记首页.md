# Figma 设计稿规范文档 - 素记首页

## 基本信息

| 属性 | 值 |
|------|-----|
| 页面名称 | 素记-首页 (Suji Homepage) |
| 设计稿URL | https://www.figma.com/design/XOxLZWsKCbK5MVpMwIdu2o/use_fimga-test?node-id=366-2&m=dev |
| 节点ID | 366-2 |
| 设备类型 | 移动端 App |
| 设计宽度 | 375px |
| 设计高度 | 812px |

---

## 页面结构概览

```
素记-首页 (366:2)
├── 状态栏 (366:3)
├── 顶部导航栏 (366:4)
│   ├── 标题 "素记" (366:5)
│   ├── 头像区域 (366:6)
│   ├── 头像文字 "U" (366:7)
│   ├── 铃铛图标 (366:8)
│   └── 消息徽章 (366:9)
├── 余额卡片 (366:10)
│   ├── "本月结余" 标签 (366:11)
│   ├── 余额金额 "¥12,580.00" (366:12)
│   ├── 增长徽章 (366:13)
│   │   └── "+12.5%" (366:14)
│   ├── 分隔线 (366:15)
│   ├── "本月收入" 标签 (366:16)
│   ├── 本月收入金额 (366:17)
│   ├── "本月支出" 标签 (366:18)
│   └── 本月支出金额 (366:19)
├── 快捷操作按钮区
│   ├── 记支出 (366:20-366:22)
│   ├── 记收入 (366:23-366:25)
│   ├── 转账 (366:26-366:28)
│   └── 预算 (366:29-366:31)
├── 最近交易区域 (366:32-366:60)
│   ├── 标题 "最近交易" (366:32)
│   ├── "查看全部" (366:33)
│   ├── 午餐-麦当劳 (366:34-366:40)
│   ├── 打车-滴滴出行 (366:41-366:47)
│   ├── 工资收入 (366:48-366:54)
│   └── 超市购物 (366:55-366:60)
└── 底部 Tab 栏 (366:61-366:65)
    ├── 首页 (366:62)
    ├── 明细 (366:63)
    ├── 报表 (366:64)
    └── 我的 (366:65)
```

---

## 组件详细规范

### 1. 状态栏 (Status Bar)

| 属性 | 值 |
|------|-----|
| 节点ID | 366:3 |
| 名称 | status-bar |
| X坐标 | 0 |
| Y坐标 | 0 |
| 宽度 | 375px |
| 高度 | 44px |
| 背景色 | #FFFFFF |

### 2. 顶部导航栏 (Header)

| 属性 | 值 |
|------|-----|
| 节点ID | 366:4 |
| 名称 | header |
| X坐标 | 0 |
| Y坐标 | 44 |
| 宽度 | 375px |
| 高度 | 56px |
| 背景色 | #FFFFFF |

### 3. 标题 "素记"

| 属性 | 值 |
|------|-----|
| 节点ID | 366:5 |
| 名称 | 素记 |
| X坐标 | 20 |
| Y坐标 | 56 |
| 宽度 | 48px |
| 高度 | 29px |
| 字体 | Inter, Noto Sans JP, Noto Sans SC, sans-serif |
| 字号 | 24px |
| 颜色 | #1A1A1A |

### 4. 用户头像区域

#### 头像

| 属性 | 值 |
|------|-----|
| 节点ID | 366:6 |
| 名称 | avatar |
| X坐标 | 315 |
| Y坐标 | 52 |
| 宽度 | 40px |
| 高度 | 40px |
| 类型 | 椭圆形 |

#### 头像文字

| 属性 | 值 |
|------|-----|
| 节点ID | 366:7 |
| 名称 | avatar-U |
| X坐标 | 329 |
| Y坐标 | 62 |
| 宽度 | 12px |
| 高度 | 19px |
| 字体 | Inter, sans-serif |
| 字号 | 16px |
| 颜色 | #999 |

#### 铃铛图标

| 属性 | 值 |
|------|-----|
| 节点ID | 366:8 |
| 名称 | bell |
| X坐标 | 275 |
| Y坐标 | 62 |
| 宽度 | 20px |
| 高度 | 20px |
| 类型 | 椭圆形 |

#### 消息徽章

| 属性 | 值 |
|------|-----|
| 节点ID | 366:9 |
| 名称 | badge |
| X坐标 | 289 |
| Y坐标 | 58 |
| 宽度 | 8px |
| 高度 | 8px |
| 类型 | 椭圆形 |

---

### 5. 余额卡片 (Balance Card)

| 属性 | 值 |
|------|-----|
| 节点ID | 366:10 |
| 名称 | balance-card |
| X坐标 | 20 |
| Y坐标 | 116 |
| 宽度 | 335px |
| 高度 | 170px |
| 背景色 | #1A1A1A |
| 圆角 | 16px |

#### 余额卡片内部元素

| 元素 | 节点ID | X | Y | 宽度 | 高度 | 字号 | 颜色 | 内容 |
|------|--------|---|----|------|------|------|------|------|
| 本月结余标签 | 366:11 | 40 | 136 | 52 | 16 | 13px | #AAA | 本月结余 |
| 余额金额 | 366:12 | 40 | 158 | 167 | 39 | 32px | #FFFFFF | ¥12,580.00 |
| 增长徽章背景 | 366:13 | 275 | 136 | 60 | 24 | - | #52C41A | - |
| 增长百分比 | 366:14 | 283 | 140 | 41 | 15 | 12px | #FFFFFF | +12.5% |
| 分隔线 | 366:15 | 40 | 210 | 295 | 1 | - | #333 | - |
| 本月收入标签 | 366:16 | 40 | 226 | 48 | 15 | 12px | #AAA | 本月收入 |
| 本月收入金额 | 366:17 | 40 | 244 | 60 | 19 | 16px | #FFFFFF | ¥18,500 |
| 本月支出标签 | 366:18 | 200 | 226 | 48 | 15 | 12px | #AAA | 本月支出 |
| 本月支出金额 | 366:19 | 200 | 244 | 53 | 19 | 16px | #FFFFFF | ¥5,920 |

---

### 6. 快捷操作按钮区

按钮区域从 X=36, Y=318 开始，水平排列，间距 83px

| 按钮 | 图标节点ID | 图标位置 | 图标尺寸 | 符号 | 符号X | 符号Y | 符号字号 | 符号颜色 | 标签X | 标签Y | 标签 |
|------|-----------|----------|----------|------|-------|-------|----------|----------|-------|-------|------|
| 记支出 | 366:20 | (36, 318) | 48x48 | − | 52 | 328 | 20px | #FF4D4F | 32 | 374 | 记支出 |
| 记收入 | 366:23 | (119, 318) | 48x48 | + | 135 | 328 | 20px | #1890FF | 115 | 374 | 记收入 |
| 转账 | 366:26 | (202, 318) | 48x48 | ⇄ | 218 | 328 | 20px | #52C41A | 198 | 374 | 转账 |
| 预算 | 366:29 | (285, 318) | 48x48 | ¥ | 301 | 328 | 20px | #FAAD14 | 281 | 374 | 预算 |

---

### 7. 最近交易区域

#### 标题栏

| 元素 | 节点ID | X | Y | 宽度 | 高度 | 字号 | 颜色 |
|------|--------|---|----|------|------|------|------|
| 最近交易 | 366:32 | 20 | 414 | 72 | 22 | 18px | #1A1A1A |
| 查看全部 | 366:33 | 290 | 416 | 56 | 17 | 14px | #999 |

#### 交易记录卡片

卡片统一尺寸: 335 x 64px

| 交易项 | 卡片节点 | 卡片位置 | 图标节点 | 图标位置 | 图标尺寸 | 图标文字 | 名称节点 | 名称位置 | 时间节点 | 时间位置 | 金额节点 | 金额位置 | 金额 | 金额颜色 |
|--------|----------|----------|----------|----------|----------|----------|----------|----------|----------|----------|----------|----------|------|----------|
| 午餐-麦当劳 | 366:34 | (20, 454) | 366:35 | (36, 468) | 36x36 | 餐 | 366:37 | (84, 466) | 366:38 | (84, 490) | 366:39 | (280, 476) | -¥58.00 | #D95C4A |
| 打车-滴滴出行 | 366:41 | (20, 528) | 366:42 | (36, 542) | 36x36 | 车 | 366:44 | (84, 540) | 366:45 | (84, 564) | 366:46 | (280, 550) | -¥32.50 | #D95C4A |
| 工资收入 | 366:48 | (20, 602) | 366:49 | (36, 616) | 36x36 | 薪 | 366:51 | (84, 614) | 366:52 | (84, 638) | 366:53 | (280, 624) | +¥15,000 | #4CAF50 |
| 超市购物 | 366:55 | (20, 676) | 366:56 | (36, 690) | 36x36 | 购 | 366:58 | (84, 688) | 366:59 | (84, 712) | 366:60 | (280, 698) | -¥128.60 | #D95C4A |

---

### 8. 底部 Tab 栏

| 属性 | 值 |
|------|-----|
| 节点ID | 366:61 |
| 名称 | tab-bar |
| X坐标 | 0 |
| Y坐标 | 728 |
| 宽度 | 375px |
| 高度 | 84px |
| 背景色 | #FFFFFF |

#### Tab 项

| Tab | 节点ID | X | Y | 宽度 | 高度 | 字号 | 颜色 | 状态 |
|-----|--------|---|----|------|------|------|------|------|
| 首页 | 366:62 | 46 | 770 | 22 | 13 | 11px | #1A1A1A | 选中 |
| 明细 | 366:63 | 131 | 770 | 22 | 13 | 11px | #CCC | 未选中 |
| 报表 | 366:64 | 216 | 770 | 22 | 13 | 11px | #CCC | 未选中 |
| 我的 | 366:65 | 301 | 770 | 22 | 13 | 11px | #CCC | 未选中 |

---

## 颜色系统

| 颜色用途 | 色值 | 使用位置 |
|----------|------|----------|
| 主文字色 | #1A1A1A | 标题、余额金额、收入金额、Tab选中 |
| 次要文字色 | #999 | 头像文字、标签文字、查看全部 |
| 三级文字色 | #CCC | Tab未选中 |
| 余额卡片背景 | #1A1A1A | 余额卡片背景 |
| 收入金额 | #4CAF50 | 工资收入金额 |
| 支出金额 | #D95C4A | 午餐、打车、超市购物金额 |
| 增长徽章 | #52C41A | 增长徽章背景、转账符号 |
| 记支出符号 | #FF4D4F | 记支出符号 |
| 记收入符号 | #1890FF | 记收入符号 |
| 预算符号 | #FAAD14 | 预算符号 |
| 分隔线深色 | #333 | 余额卡片内分隔线 |
| 分隔线浅色 | #F0F0EE | 交易记录分隔线 |
| 状态栏背景 | #FFFFFF | 状态栏 |
| 导航栏背景 | #FFFFFF | 导航栏 |
| Tab栏背景 | #FFFFFF | Tab栏 |

---

## 字体系统

| 字体栈 |
|--------|
| Inter |
| Noto Sans JP |
| Noto Sans SC |
| Noto Sans Math |
| sans-serif |

---

## React 代码参考 (来自 Figma MCP)

```jsx
const imgAvatar = "http://localhost:3845/assets/5e87e69a0a050a9e1a706684e4d772dc47fc99a4.svg";
const imgBell = "http://localhost:3845/assets/0ff63c8c337d6675c3015f55ff43ecc50a1c8c55.svg";
const imgBadge = "http://localhost:3845/assets/1c44552485aacca27ce586fb534c34c83faecd93.svg";
const imgIcon = "http://localhost:3845/assets/400faac8c09b90834758631eb55710de7943f716.svg";
const imgIcon1 = "http://localhost:3845/assets/bee6255672a8a8eda070b5691b42f432a02f328b.svg";
const imgIcon2 = "http://localhost:3845/assets/8155d7e09bbfc936fe0cb1d063e34f4795662a4b.svg";
const imgIcon3 = "http://localhost:3845/assets/45933729323263023a6ad17c604e75ea25aac666.svg";
const imgIcon4 = "http://localhost:3845/assets/f8951818b81a9bcae84e19cb644e2dcf654bc357.svg";
const imgIcon5 = "http://localhost:3845/assets/7c682b0e41c3dc55112d5360f6e4436459149650.svg";
const imgIcon6 = "http://localhost:3845/assets/298a82db40c130321b78b7da672e66d4cb795844.svg";
const imgIcon7 = "http://localhost:3845/assets/364eba2ea73d0c11cd8640af266e818d90dcbcfd.svg";

export default function Component() {
  return (
    <div className="bg-[#f7f7f5] relative size-full" data-node-id="366:2" data-name="素记-首页">
      <div className="absolute bg-white h-[44px] left-0 top-0 w-[375px]" data-node-id="366:3" data-name="status-bar" />
      <div className="absolute bg-white h-[56px] left-0 top-[44px] w-[375px]" data-node-id="366:4" data-name="header" />
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[20px] not-italic text-[#1a1a1a] text-[24px] top-[56px] whitespace-nowrap" data-node-id="366:5">
        素记
      </p>
      <div className="absolute left-[315px] size-[40px] top-[52px]" data-node-id="366:6" data-name="avatar">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgAvatar} />
      </div>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[329px] not-italic text-[#999] text-[16px] top-[62px] whitespace-nowrap" data-node-id="366:7">
        U
      </p>
      <div className="absolute left-[275px] size-[20px] top-[62px]" data-node-id="366:8" data-name="bell">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgBell} />
      </div>
      <div className="absolute left-[289px] size-[8px] top-[58px]" data-node-id="366:9" data-name="badge">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgBadge} />
      </div>
      <div className="absolute bg-[#1a1a1a] h-[170px] left-[20px] rounded-[16px] top-[116px] w-[335px]" data-node-id="366:10" data-name="balance-card" />
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[#aaa] text-[13px] top-[136px] whitespace-nowrap" data-node-id="366:11">
        本月结余
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[32px] text-white top-[158px] whitespace-nowrap" data-node-id="366:12">
        ¥12,580.00
      </p>
      <div className="absolute bg-[#52c41a] h-[24px] left-[275px] rounded-[4px] top-[136px] w-[60px]" data-node-id="366:13" data-name="growth-badge" />
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[283px] not-italic text-[12px] text-white top-[140px] whitespace-nowrap" data-node-id="366:14">
        +12.5%
      </p>
      <div className="absolute bg-[#333] h-px left-[40px] top-[210px] w-[295px]" data-node-id="366:15" data-name="divider" />
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[#aaa] text-[12px] top-[226px] whitespace-nowrap" data-node-id="366:16">
        本月收入
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[16px] text-white top-[244px] whitespace-nowrap" data-node-id="366:17">
        ¥18,500
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[200px] not-italic text-[#aaa] text-[12px] top-[226px] whitespace-nowrap" data-node-id="366:18">
        本月支出
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[200px] not-italic text-[16px] text-white top-[244px] whitespace-nowrap" data-node-id="366:19">
        ¥5,920
      </p>
      <div className="absolute left-[36px] size-[48px] top-[318px]" data-node-id="366:20" data-name="记支出-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon} />
      </div>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[52px] not-italic text-[#ff4d4f] text-[20px] top-[328px] whitespace-nowrap" data-node-id="366:21">
        −
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[32px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap" data-node-id="366:22">
        记支出
      </p>
      <div className="absolute left-[119px] size-[48px] top-[318px]" data-node-id="366:23" data-name="记收入-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon1} />
      </div>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[135px] not-italic text-[#1890ff] text-[20px] top-[328px] whitespace-nowrap" data-node-id="366:24">
        +
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[115px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap" data-node-id="366:25">
        记收入
      </p>
      <div className="absolute left-[202px] size-[48px] top-[318px]" data-node-id="366:26" data-name="转账-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon2} />
      </div>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[218px] not-italic text-[#52c41a] text-[20px] top-[328px] whitespace-nowrap" data-node-id="366:27">
        ⇄
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[198px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap" data-node-id="366:28">
        转账
      </p>
      <div className="absolute left-[285px] size-[48px] top-[318px]" data-node-id="366:29" data-name="预算-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon3} />
      </div>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[301px] not-italic text-[#faad14] text-[20px] top-[328px] whitespace-nowrap" data-node-id="366:30">
        ¥
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[281px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap" data-node-id="366:31">
        预算
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[20px] not-italic text-[#1a1a1a] text-[18px] top-[414px] whitespace-nowrap" data-node-id="366:32">
        最近交易
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[290px] not-italic text-[#999] text-[14px] top-[416px] whitespace-nowrap" data-node-id="366:33">
        查看全部
      </p>
      <div className="absolute bg-white h-[64px] left-[20px] top-[454px] w-[335px]" data-node-id="366:34" data-name="午餐 - 麦当劳-card" />
      <div className="absolute left-[36px] size-[36px] top-[468px]" data-node-id="366:35" data-name="午餐 - 麦当劳-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon4} />
      </div>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[476px] whitespace-nowrap" data-node-id="366:36">
        餐
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[466px] whitespace-nowrap" data-node-id="366:37">
        午餐 - 麦当劳
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[490px] whitespace-nowrap" data-node-id="366:38">
        今天 12:30
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#d95c4a] text-[15px] top-[476px] whitespace-nowrap" data-node-id="366:39">
        -¥58.00
      </p>
      <div className="absolute bg-[#f0f0ee] h-px left-[20px] top-[518px] w-[335px]" data-node-id="366:40" data-name="午餐 - 麦当劳-divider" />
      <div className="absolute bg-white h-[64px] left-[20px] top-[528px] w-[335px]" data-node-id="366:41" data-name="打车 - 滴滴出行-card" />
      <div className="absolute left-[36px] size-[36px] top-[542px]" data-node-id="366:42" data-name="打车 - 滴滴出行-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon5} />
      </div>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[550px] whitespace-nowrap" data-node-id="366:43">
        车
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[540px] whitespace-nowrap" data-node-id="366:44">
        打车 - 滴滴出行
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[564px] whitespace-nowrap" data-node-id="366:45">
        今天 08:45
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#d95c4a] text-[15px] top-[550px] whitespace-nowrap" data-node-id="366:46">
        -¥32.50
      </p>
      <div className="absolute bg-[#f0f0ee] h-px left-[20px] top-[592px] w-[335px]" data-node-id="366:47" data-name="打车 - 滴滴出行-divider" />
      <div className="absolute bg-white h-[64px] left-[20px] top-[602px] w-[335px]" data-node-id="366:48" data-name="工资收入-card" />
      <div className="absolute left-[36px] size-[36px] top-[616px]" data-node-id="366:49" data-name="工资收入-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon6} />
      </div>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[624px] whitespace-nowrap" data-node-id="366:50">
        薪
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[614px] whitespace-nowrap" data-node-id="366:51">
        工资收入
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[638px] whitespace-nowrap" data-node-id="366:52">
        昨天
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#4caf50] text-[15px] top-[624px] whitespace-nowrap" data-node-id="366:53">
        +¥15,000
      </p>
      <div className="absolute bg-[#f0f0ee] h-px left-[20px] top-[666px] w-[335px]" data-node-id="366:54" data-name="工资收入-divider" />
      <div className="absolute bg-white h-[64px] left-[20px] top-[676px] w-[335px]" data-node-id="366:55" data-name="超市购物-card" />
      <div className="absolute left-[36px] size-[36px] top-[690px]" data-node-id="366:56" data-name="超市购物-icon">
        <img alt="" className="absolute block inset-0 max-w-none size-full" src={imgIcon7} />
      </div>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[698px] whitespace-nowrap" data-node-id="366:57">
        购
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[688px] whitespace-nowrap" data-node-id="366:58">
        超市购物
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[712px] whitespace-nowrap" data-node-id="366:59">
        昨天 19:20
      </p>
      <p className="absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#d95c4a] text-[15px] top-[698px] whitespace-nowrap" data-node-id="366:60">
        -¥128.60
      </p>
      <div className="absolute bg-white h-[84px] left-0 top-[728px] w-[375px]" data-node-id="366:61" data-name="tab-bar" />
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[11px] top-[770px] whitespace-nowrap" data-node-id="366:62">
        首页
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[131px] not-italic text-[#ccc] text-[11px] top-[770px] whitespace-nowrap" data-node-id="366:63">
        明细
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[216px] not-italic text-[#ccc] text-[11px] top-[770px] whitespace-nowrap" data-node-id="366:64">
        报表
      </p>
      <p className="absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[301px] not-italic text-[#ccc] text-[11px] top-[770px] whitespace-nowrap" data-node-id="366:65">
        我的
      </p>
    </div>
  );
}
```

---

## 设计说明

1. **移动端优先设计**: 设计稿针对375px宽度优化，采用固定布局
2. **卡片式布局**: 使用圆角卡片组织相关内容
3. **色彩语义化**: 绿色表示收入/正向，红色/橙色表示支出/负向
4. **简洁的视觉层次**: 通过字号大小和颜色深浅区分信息优先级
5. **底部Tab导航**: 采用常见的4Tab结构（首页、明细、报表、我的）

---

## Figma MCP 原始返回数据

### mcp_figma_get_design_context 原始返回

```json
[{"type":"text","data":"const imgAvatar = \"http://localhost:3845/assets/5e87e69a0a050a9e1a706684e4d772dc47fc99a4.svg\";\nconst imgBell = \"http://localhost:3845/assets/0ff63c8c337d6675c3015f55ff43ecc50a1c8c55.svg\";\nconst imgBadge = \"http://localhost:3845/assets/1c44552485aacca27ce586fb534c34c83faecd93.svg\";\nconst imgIcon = \"http://localhost:3845/assets/400faac8c09b90834758631eb55710de7943f716.svg\";\nconst imgIcon1 = \"http://localhost:3845/assets/bee6255672a8a8eda070b5691b42f432a02f328b.svg\";\nconst imgIcon2 = \"http://localhost:3845/assets/8155d7e09bbfc936fe0cb1d063e34f4795662a4b.svg\";\nconst imgIcon3 = \"http://localhost:3845/assets/45933729323263023a6ad17c604e75ea25aac666.svg\";\nconst imgIcon4 = \"http://localhost:3845/assets/f8951818b81a9bcae84e19cb644e2dcf654bc357.svg\";\nconst imgIcon5 = \"http://localhost:3845/assets/7c682b0e41c3dc55112d5360f6e4436459149650.svg\";\nconst imgIcon6 = \"http://localhost:3845/assets/298a82db40c130321b78b7da672e66d4cb795844.svg\";\nconst imgIcon7 = \"http://localhost:3845/assets/364eba2ea73d0c11cd8640af266e818d90dcbcfd.svg\";\n\nexport default function Component() {\n  return (\n    <div className=\"bg-[#f7f7f5] relative size-full\" data-node-id=\"366:2\" data-name=\"素记-首页\">\n      <div className=\"absolute bg-white h-[44px] left-0 top-0 w-[375px]\" data-node-id=\"366:3\" data-name=\"status-bar\" />\n      <div className=\"absolute bg-white h-[56px] left-0 top-[44px] w-[375px]\" data-node-id=\"366:4\" data-name=\"header\" />\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[20px] not-italic text-[#1a1a1a] text-[24px] top-[56px] whitespace-nowrap\" data-node-id=\"366:5\">\n        素记\n      </p>\n      <div className=\"absolute left-[315px] size-[40px] top-[52px]\" data-node-id=\"366:6\" data-name=\"avatar\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgAvatar} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[329px] not-italic text-[#999] text-[16px] top-[62px] whitespace-nowrap\" data-node-id=\"366:7\">\n        U\n      </p>\n      <div className=\"absolute left-[275px] size-[20px] top-[62px]\" data-node-id=\"366:8\" data-name=\"bell\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgBell} />\n      </div>\n      <div className=\"absolute left-[289px] size-[8px] top-[58px]\" data-node-id=\"366:9\" data-name=\"badge\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgBadge} />\n      </div>\n      <div className=\"absolute bg-[#1a1a1a] h-[170px] left-[20px] rounded-[16px] top-[116px] w-[335px]\" data-node-id=\"366:10\" data-name=\"balance-card\" />\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[#aaa] text-[13px] top-[136px] whitespace-nowrap\" data-node-id=\"366:11\">\n        本月结余\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[32px] text-white top-[158px] whitespace-nowrap\" data-node-id=\"366:12\">\n        ¥12,580.00\n      </p>\n      <div className=\"absolute bg-[#52c41a] h-[24px] left-[275px] rounded-[4px] top-[136px] w-[60px]\" data-node-id=\"366:13\" data-name=\"growth-badge\" />\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[283px] not-italic text-[12px] text-white top-[140px] whitespace-nowrap\" data-node-id=\"366:14\">\n        +12.5%\n      </p>\n      <div className=\"absolute bg-[#333] h-px left-[40px] top-[210px] w-[295px]\" data-node-id=\"366:15\" data-name=\"divider\" />\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[#aaa] text-[12px] top-[226px] whitespace-nowrap\" data-node-id=\"366:16\">\n        本月收入\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[40px] not-italic text-[16px] text-white top-[244px] whitespace-nowrap\" data-node-id=\"366:17\">\n        ¥18,500\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[200px] not-italic text-[#aaa] text-[12px] top-[226px] whitespace-nowrap\" data-node-id=\"366:18\">\n        本月支出\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[200px] not-italic text-[16px] text-white top-[244px] whitespace-nowrap\" data-node-id=\"366:19\">\n        ¥5,920\n      </p>\n      <div className=\"absolute left-[36px] size-[48px] top-[318px]\" data-node-id=\"366:20\" data-name=\"记支出-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[52px] not-italic text-[#ff4d4f] text-[20px] top-[328px] whitespace-nowrap\" data-node-id=\"366:21\">\n        −\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[32px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap\" data-node-id=\"366:22\">\n        记支出\n      </p>\n      <div className=\"absolute left-[119px] size-[48px] top-[318px]\" data-node-id=\"366:23\" data-name=\"记收入-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon1} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[135px] not-italic text-[#1890ff] text-[20px] top-[328px] whitespace-nowrap\" data-node-id=\"366:24\">\n        +\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[115px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap\" data-node-id=\"366:25\">\n        记收入\n      </p>\n      <div className=\"absolute left-[202px] size-[48px] top-[318px]\" data-node-id=\"366:26\" data-name=\"转账-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon2} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[218px] not-italic text-[#52c41a] text-[20px] top-[328px] whitespace-nowrap\" data-node-id=\"366:27\">\n        ⇄\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[198px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap\" data-node-id=\"366:28\">\n        转账\n      </p>\n      <div className=\"absolute left-[285px] size-[48px] top-[318px]\" data-node-id=\"366:29\" data-name=\"预算-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon3} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[301px] not-italic text-[#faad14] text-[20px] top-[328px] whitespace-nowrap\" data-node-id=\"366:30\">\n        ¥\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[281px] not-italic text-[#999] text-[12px] top-[374px] whitespace-nowrap\" data-node-id=\"366:31\">\n        预算\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[20px] not-italic text-[#1a1a1a] text-[18px] top-[414px] whitespace-nowrap\" data-node-id=\"366:32\">\n        最近交易\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[290px] not-italic text-[#999] text-[14px] top-[416px] whitespace-nowrap\" data-node-id=\"366:33\">\n        查看全部\n      </p>\n      <div className=\"absolute bg-white h-[64px] left-[20px] top-[454px] w-[335px]\" data-node-id=\"366:34\" data-name=\"午餐 - 麦当劳-card\" />\n      <div className=\"absolute left-[36px] size-[36px] top-[468px]\" data-node-id=\"366:35\" data-name=\"午餐 - 麦当劳-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon4} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[476px] whitespace-nowrap\" data-node-id=\"366:36\">\n        餐\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[466px] whitespace-nowrap\" data-node-id=\"366:37\">\n        午餐 - 麦当劳\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[490px] whitespace-nowrap\" data-node-id=\"366:38\">\n        今天 12:30\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#d95c4a] text-[15px] top-[476px] whitespace-nowrap\" data-node-id=\"366:39\">\n        -¥58.00\n      </p>\n      <div className=\"absolute bg-[#f0f0ee] h-px left-[20px] top-[518px] w-[335px]\" data-node-id=\"366:40\" data-name=\"午餐 - 麦当劳-divider\" />\n      <div className=\"absolute bg-white h-[64px] left-[20px] top-[528px] w-[335px]\" data-node-id=\"366:41\" data-name=\"打车 - 滴滴出行-card\" />\n      <div className=\"absolute left-[36px] size-[36px] top-[542px]\" data-node-id=\"366:42\" data-name=\"打车 - 滴滴出行-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon5} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[550px] whitespace-nowrap\" data-node-id=\"366:43\">\n        车\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[540px] whitespace-nowrap\" data-node-id=\"366:44\">\n        打车 - 滴滴出行\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[564px] whitespace-nowrap\" data-node-id=\"366:45\">\n        今天 08:45\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#d95c4a] text-[15px] top-[550px] whitespace-nowrap\" data-node-id=\"366:46\">\n        -¥32.50\n      </p>\n      <div className=\"absolute bg-[#f0f0ee] h-px left-[20px] top-[592px] w-[335px]\" data-node-id=\"366:47\" data-name=\"打车 - 滴滴出行-divider\" />\n      <div className=\"absolute bg-white h-[64px] left-[20px] top-[602px] w-[335px]\" data-node-id=\"366:48\" data-name=\"工资收入-card\" />\n      <div className=\"absolute left-[36px] size-[36px] top-[616px]\" data-node-id=\"366:49\" data-name=\"工资收入-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon6} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[624px] whitespace-nowrap\" data-node-id=\"366:50\">\n        薪\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[614px] whitespace-nowrap\" data-node-id=\"366:51\">\n        工资收入\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[638px] whitespace-nowrap\" data-node-id=\"366:52\">\n        昨天\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#4caf50] text-[15px] top-[624px] whitespace-nowrap\" data-node-id=\"366:53\">\n        +¥15,000\n      </p>\n      <div className=\"absolute bg-[#f0f0ee] h-px left-[20px] top-[666px] w-[335px]\" data-node-id=\"366:54\" data-name=\"工资收入-divider\" />\n      <div className=\"absolute bg-white h-[64px] left-[20px] top-[676px] w-[335px]\" data-node-id=\"366:55\" data-name=\"超市购物-card\" />\n      <div className=\"absolute left-[36px] size-[36px] top-[690px]\" data-node-id=\"366:56\" data-name=\"超市购物-icon\">\n        <img alt=\"\" className=\"absolute block inset-0 max-w-none size-full\" src={imgIcon7} />\n      </div>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[14px] top-[698px] whitespace-nowrap\" data-node-id=\"366:57\">\n        购\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#1a1a1a] text-[15px] top-[688px] whitespace-nowrap\" data-node-id=\"366:58\">\n        超市购物\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[84px] not-italic text-[#999] text-[12px] top-[712px] whitespace-nowrap\" data-node-id=\"366:59\">\n        昨天 19:20\n      </p>\n      <p className=\"absolute font-['Inter:Regular',sans-serif] font-normal leading-[normal] left-[280px] not-italic text-[#d95c4a] text-[15px] top-[698px] whitespace-nowrap\" data-node-id=\"366:60\">\n        -¥128.60\n      </p>\n      <div className=\"absolute bg-white h-[84px] left-0 top-[728px] w-[375px]\" data-node-id=\"366:61\" data-name=\"tab-bar\" />\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[46px] not-italic text-[#1a1a1a] text-[11px] top-[770px] whitespace-nowrap\" data-node-id=\"366:62\">\n        首页\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular','Noto_Sans_SC:Regular',sans-serif] font-normal leading-[normal] left-[131px] not-italic text-[#ccc] text-[11px] top-[770px] whitespace-nowrap\" data-node-id=\"366:63\">\n        明细\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_SC:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[216px] not-italic text-[#ccc] text-[11px] top-[770px] whitespace-nowrap\" data-node-id=\"366:64\">\n        报表\n      </p>\n      <p className=\"absolute font-['Inter:Regular','Noto_Sans_JP:Regular',sans-serif] font-normal leading-[normal] left-[301px] not-italic text-[#ccc] text-[11px] top-[770px] whitespace-nowrap\" data-node-id=\"366:65\">\n        我的\n      </p>\n    </div>\n  );\n}"},{"type":"text","data":"SUPER CRITICAL: The generated React+Tailwind code MUST be converted to match the target project's technology stack and styling system.\n1. Analyze the target codebase to identify: technology stack, styling approach, component patterns, and design tokens\n2. Convert React syntax to the target framework/library\n3. Transform all Tailwind classes to the target styling system while preserving exact visual design\n4. Follow the project's existing patterns and conventions\nDO NOT install any Tailwind as a dependency unless the user instructs you to do so.\n"},{"type":"text","data":"Node ids have been added to the code as data attributes, e.g. `data-node-id=\"1:2\"`."},{"type":"text","data":"Image assets are stored on a localhost server. Clients can use these images directly in code as a way to view the image assets the same way they would other remote servers. Images and SVGs will be stored as constants, e.g. const image = 'http://localhost:3845/assets/10c13ac1a228a365cb98a0064b1d5afbc84887b2.png' These constants will be used in the code as the source for the image, e.g. <img src={image} /> This is true for both images and SVGs, so you can use the same approach for both types of assets."},{"type":"text","data":"IMPORTANT: After you call this tool, you MUST call get_screenshot to get a screenshot of the node for context."}]"}
```

### mcp_figma_get_metadata 原始返回

```json
[{"type":"text","data":"<frame id=\"366:2\" name=\"素记-首页\" x=\"12\" y=\"0\" width=\"375\" height=\"812\">\n  <rounded-rectangle id=\"366:3\" name=\"status-bar\" x=\"0\" y=\"0\" width=\"375\" height=\"44\" />\n  <rounded-rectangle id=\"366:4\" name=\"header\" x=\"0\" y=\"44\" width=\"375\" height=\"56\" />\n  <text id=\"366:5\" name=\"素记\" x=\"20\" y=\"56\" width=\"48\" height=\"29\" />\n  <ellipse id=\"366:6\" name=\"avatar\" x=\"315\" y=\"52\" width=\"40\" height=\"40\" />\n  <text id=\"366:7\" name=\"avatar-U\" x=\"329\" y=\"62\" width=\"12\" height=\"19\" />\n  <ellipse id=\"366:8\" name=\"bell\" x=\"275\" y=\"62\" width=\"20\" height=\"20\" />\n  <ellipse id=\"366:9\" name=\"badge\" x=\"289\" y=\"58\" width=\"8\" height=\"8\" />\n  <rounded-rectangle id=\"366:10\" name=\"balance-card\" x=\"20\" y=\"116\" width=\"335\" height=\"170\" />\n  <text id=\"366:11\" name=\"本月结余\" x=\"40\" y=\"136\" width=\"52\" height=\"16\" />\n  <text id=\"366:12\" name=\"余额金额\" x=\"40\" y=\"158\" width=\"167\" height=\"39\" />\n  <rounded-rectangle id=\"366:13\" name=\"growth-badge\" x=\"275\" y=\"136\" width=\"60\" height=\"24\" />\n  <text id=\"366:14\" name=\"+12.5%\" x=\"283\" y=\"140\" width=\"41\" height=\"15\" />\n  <rounded-rectangle id=\"366:15\" name=\"divider\" x=\"40\" y=\"210\" width=\"295\" height=\"1\" />\n  <text id=\"366:16\" name=\"本月收入-label\" x=\"40\" y=\"226\" width=\"48\" height=\"15\" />\n  <text id=\"366:17\" name=\"本月收入-value\" x=\"40\" y=\"244\" width=\"60\" height=\"19\" />\n  <text id=\"366:18\" name=\"本月支出-label\" x=\"200\" y=\"226\" width=\"48\" height=\"15\" />\n  <text id=\"366:19\" name=\"本月支出-value\" x=\"200\" y=\"244\" width=\"53\" height=\"19\" />\n  <ellipse id=\"366:20\" name=\"记支出-icon\" x=\"36\" y=\"318\" width=\"48\" height=\"48\" />\n  <text id=\"366:21\" name=\"记支出-symbol\" x=\"52\" y=\"328\" width=\"14\" height=\"24\" />\n  <text id=\"366:22\" name=\"记支出-label\" x=\"32\" y=\"374\" width=\"36\" height=\"15\" />\n  <ellipse id=\"366:23\" name=\"记收入-icon\" x=\"119\" y=\"318\" width=\"48\" height=\"48\" />\n  <text id=\"366:24\" name=\"记收入-symbol\" x=\"135\" y=\"328\" width=\"14\" height=\"24\" />\n  <text id=\"366:25\" name=\"记收入-label\" x=\"115\" y=\"374\" width=\"36\" height=\"15\" />\n  <ellipse id=\"366:26\" name=\"转账-icon\" x=\"202\" y=\"318\" width=\"48\" height=\"48\" />\n  <text id=\"366:27\" name=\"转账-symbol\" x=\"218\" y=\"328\" width=\"21\" height=\"24\" />\n  <text id=\"366:28\" name=\"转账-label\" x=\"198\" y=\"374\" width=\"24\" height=\"15\" />\n  <ellipse id=\"366:29\" name=\"预算-icon\" x=\"285\" y=\"318\" width=\"48\" height=\"48\" />\n  <text id=\"366:30\" name=\"预算-symbol\" x=\"301\" y=\"328\" width=\"11\" height=\"24\" />\n  <text id=\"366:31\" name=\"预算-label\" x=\"281\" y=\"374\" width=\"24\" height=\"15\" />\n  <text id=\"366:32\" name=\"最近交易\" x=\"20\" y=\"414\" width=\"72\" height=\"22\" />\n  <text id=\"366:33\" name=\"查看全部\" x=\"290\" y=\"416\" width=\"56\" height=\"17\" />\n  <rounded-rectangle id=\"366:34\" name=\"午餐 - 麦当劳-card\" x=\"20\" y=\"454\" width=\"335\" height=\"64\" />\n  <ellipse id=\"366:35\" name=\"午餐 - 麦当劳-icon\" x=\"36\" y=\"468\" width=\"36\" height=\"36\" />\n  <text id=\"366:36\" name=\"午餐 - 麦当劳-icon-text\" x=\"46\" y=\"476\" width=\"14\" height=\"17\" />\n  <text id=\"366:37\" name=\"午餐 - 麦当劳-name\" x=\"84\" y=\"466\" width=\"91\" height=\"18\" />\n  <text id=\"366:38\" name=\"午餐 - 麦当劳-time\" x=\"84\" y=\"490\" width=\"59\" height=\"15\" />\n  <text id=\"366:39\" name=\"午餐 - 麦当劳-amount\" x=\"280\" y=\"476\" width=\"56\" height=\"18\" />\n  <rounded-rectangle id=\"366:40\" name=\"午餐 - 麦当劳-divider\" x=\"20\" y=\"518\" width=\"335\" height=\"1\" />\n  <rounded-rectangle id=\"366:41\" name=\"打车 - 滴滴出行-card\" x=\"20\" y=\"528\" width=\"335\" height=\"64\" />\n  <ellipse id=\"366:42\" name=\"打车 - 滴滴出行-icon\" x=\"36\" y=\"542\" width=\"36\" height=\"36\" />\n  <text id=\"366:43\" name=\"打车 - 滴滴出行-icon-text\" x=\"46\" y=\"550\" width=\"14\" height=\"17\" />\n  <text id=\"366:44\" name=\"打车 - 滴滴出行-name\" x=\"84\" y=\"540\" width=\"106\" height=\"18\" />\n  <text id=\"366:45\" name=\"打车 - 滴滴出行-time\" x=\"84\" y=\"564\" width=\"61\" height=\"15\" />\n  <text id=\"366:46\" name=\"打车 - 滴滴出行-amount\" x=\"280\" y=\"550\" width=\"57\" height=\"18\" />\n  <rounded-rectangle id=\"366:47\" name=\"打车 - 滴滴出行-divider\" x=\"20\" y=\"592\" width=\"335\" height=\"1\" />\n  <rounded-rectangle id=\"366:48\" name=\"工资收入-card\" x=\"20\" y=\"602\" width=\"335\" height=\"64\" />\n  <ellipse id=\"366:49\" name=\"工资收入-icon\" x=\"36\" y=\"616\" width=\"36\" height=\"36\" />\n  <text id=\"366:50\" name=\"工资收入-icon-text\" x=\"46\" y=\"624\" width=\"14\" height=\"17\" />\n  <text id=\"366:51\" name=\"工资收入-name\" x=\"84\" y=\"614\" width=\"60\" height=\"18\" />\n  <text id=\"366:52\" name=\"工资收入-time\" x=\"84\" y=\"638\" width=\"24\" height=\"15\" />\n  <text id=\"366:53\" name=\"工资收入-amount\" x=\"280\" y=\"624\" width=\"66\" height=\"18\" />\n  <rounded-rectangle id=\"366:54\" name=\"工资收入-divider\" x=\"20\" y=\"666\" width=\"335\" height=\"1\" />\n  <rounded-rectangle id=\"366:55\" name=\"超市购物-card\" x=\"20\" y=\"676\" width=\"335\" height=\"64\" />\n  <ellipse id=\"366:56\" name=\"超市购物-icon\" x=\"36\" y=\"690\" width=\"36\" height=\"36\" />\n  <text id=\"366:57\" name=\"超市购物-icon-text\" x=\"46\" y=\"698\" width=\"14\" height=\"17\" />\n  <text id=\"366:58\" name=\"超市购物-name\" x=\"84\" y=\"688\" width=\"60\" height=\"18\" />\n  <text id=\"366:59\" name=\"超市购物-time\" x=\"84\" y=\"712\" width=\"59\" height=\"15\" />\n  <text id=\"366:60\" name=\"超市购物-amount\" x=\"280\" y=\"698\" width=\"63\" height=\"18\" />\n  <rounded-rectangle id=\"366:61\" name=\"tab-bar\" x=\"0\" y=\"728\" width=\"375\" height=\"84\" />\n  <text id=\"366:62\" name=\"首页\" x=\"46\" y=\"770\" width=\"22\" height=\"13\" />\n  <text id=\"366:63\" name=\"明细\" x=\"131\" y=\"770\" width=\"22\" height=\"13\" />\n  <text id=\"366:64\" name=\"报表\" x=\"216\" y=\"770\" width=\"22\" height=\"13\" />\n  <text id=\"366:65\" name=\"我的\" x=\"301\" y=\"770\" width=\"22\" height=\"13\" />\n</frame>"},{"type":"text","data":"IMPORTANT: After you call this tool, you MUST call get_design_context if trying to implement the design, since this tool only returns metadata. If you do not call get_design_context, the agent will not be able to implement the design."}]"}
```
