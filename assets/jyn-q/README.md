# 鲸鱼娘·Q版（jyn-q）

由 stushansusu（涂山苏苏）为 DSH 桌面宠物制作的 frames2d 内置宠物：**一张静态立绘**驱动的
程序化桌宠，8 条轨道共 **144 帧**，全部由「以脚底为锚点的缩放 + 旋转 + 位移」现场算出来。
宠物本体没有使用视频产线，也没有引用任何第三方美术素材。

## 来源

- **角色**：「鲸鱼娘」Q 版 —— 与 `assets/jyn`（女仆鲸鱼娘）、`assets/jyn-foxtail` 同一条角色线的
  Q 版形象。
- **立绘**：一张 **AI 生成的 1536×1536 透明 PNG**，由作者用**豆包（Doubao）**生成后提供。
  源文件自带 TC260 AIGC 标识（XMP `TC260:AIGC`），其中
  `ContentProducer=doubao`、`ProduceID=20b1aba58f8947a8945ff5237807539c`；
  作者侧留存的原文件名是 `DeepSeek 鲸鱼娘 Q版形象.png`。
- **母版**：`assets/jyn-q/` 的帧由该图按 alpha 包围盒裁切、LANCZOS 等比缩放到 486×640 的母版
  （由 `jynq-pet/tools/make_sprite.py` 生成）变换而来。母版与生成脚本不在本仓库内。
- **帧**：`build_jyn_q.py` 逐帧作仿射变换（预乘 alpha，2 倍超采样后 LANCZOS 缩回），
  8 条轨道共 144 帧，单元统一 **340×430**。144 帧**没有任何两帧逐字节相同**。
- **清晰度（把话说准）**：显示尺寸按**单元高**缩放（`spriteScale = display.size / cell.height`），
  不是按立绘本体缩放；立绘只占单元中间一段（逐帧实测 254–383px 高，中位 331px）。因此默认
  `size=160` 时立绘实际约 **123 CSS px** 高，2 倍屏上是源图的 **0.74 倍**下采样——**不是 1:1**。
  单元全高要 1:1 需 `size = 单元高 / 2 = 215`。

## 授权与声明

- **贡献与许可**：本宠物（帧序列、`pet.json`、`previews/`）由 stushansusu 以 **MIT** 许可证贡献；
  manifest 记 `author: 11726`、`license: MIT`。`11726` 与 stushansusu 是同一作者的两个署名
  —— 这条角色线上资产的 `pet.json` 都记 `11726`（jyn / jyn-foxtail / doro 亦然），
  而 `THIRD_PARTY_NOTICES.md` 与 README 的内置宠物表按 GitHub 账号记 stushansusu，
  Doro 条目已先记录过这一对应关系。
- **角色权利**：「鲸鱼娘」是对 DeepSeek 鲸鱼标识的**非官方同人**形象，DeepSeek 名称与鲸鱼标识的
  相关权利归 DeepSeek 所有。本作为粉丝二创，与 DeepSeek 无隶属或背书关系，不表述为官方作品，
  也不主张角色本身的任何权利。
- **使用范围**：仅限个人非商业使用——不得售卖、不得商用、不得表述为官方；本仓库 MIT 覆盖的是
  贡献者产出的帧序列与清单，不覆盖角色本身。
- 第三方声明见仓库根 `THIRD_PARTY_NOTICES.md` 的 **Jyn-Q pet (assets/jyn-q/)** 条目。
