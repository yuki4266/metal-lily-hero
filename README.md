# metal-lily-hero

个人主页。首屏是一束用 three.js 参数化建模的金属百合，随滚动生长；下面是按时间线排列的 Experience。排版参考 benjaminhardman.com：Space Grotesk 字体、居中标题、大量留白。

## 运行

线上：<https://yuki4266.github.io/metal-lily-hero/>（GitHub Pages，推 `main` 自动更新）。

单文件，无构建。three.js 通过 jsdelivr importmap 加载，字体来自 Google Fonts，需要联网。

```bash
# 直接双击 index.html，或者起个本地服务
python3 -m http.server 8791 --directory .
# 然后打开 http://127.0.0.1:8791/index.html
```

## 页面

| 区块 | 内容 |
| --- | --- |
| 首屏 | 左：金属百合花束（3 朵弯曲百合、2 花苞、小花簇、叶片、2 蝴蝶），滚动驱动生长；右：`ruxinxue` / Duke ECE · 2nd year / SDE · Agent development · Full-stack；花旁四条技术标注 |
| Experience | 时间线，从新到旧：05/2026–08/2026 Anyura（Software Engineer Intern，California，链接到官网）、01/2025–08/2025 Buka Tech。滚动时左侧竖线填充、节点点亮、条目淡入 |

改文案直接改 `index.html` 里的 HTML：首屏文字在 `.intro`，经历在 `.timeline` 的每个 `.entry`。

## 交互

| 操作 | 效果 |
| --- | --- |
| 滚动 | 首屏 320vh 内驱动生长；再往下进入 Experience |
| 鼠标拖拽首屏画布 | 360° 旋转花束，背面与正面同样完整 |
| 顶栏 `pearl / holo / chrome` | 材质预设：珍珠白金属 / 全息虹彩 / 镜面铬。默认 pearl，记在 localStorage |
| 顶栏 ☀ / ☾ | 亮 / 暗主题，默认亮色，记在 localStorage |

## 建模代码

| 函数 | 说明 |
| --- | --- |
| `petalFn(open, opts)` | 百合花瓣参数曲面（披针形：基部并成花筒、中段张开、尖端后翻，中脉龙骨，尖端三分之一两道浅波）；`open=0` 花苞、`1` 盛开，两态作为 morph target 混合实现开花；`curl` 后翻程度、`twist` / `sweep` 轻微扭转侧弯 |
| `shell(geo, d)` | 给花瓣加一层内壳，边缘有厚度感 |
| `bendGeometry / bakeBend` | 整朵花沿轴线弯成弧，默认朝重力方向 |
| `curvy(pts, amp, seed)` | 茎的控制点加 S 形摆动 |
| `leafFn / smallPetalFn` | 披针形叶片、五瓣小花 |
| `tubeGeo` + `growMaterial` | 收细的茎管 + 注入 `uGrow` 的材质，片元 `vAlong > uGrow` 时 discard |
| `STEMS` / `BUTTERFLIES` | 花束布局与生长时间窗 |
| `PRESETS` / `acidEnv()` | 三种材质预设；holo 用彩色面板生成的 PMREM 环境 |
| `TAGS` | 花旁技术标注：3D 锚点 + 屏幕偏移，每帧投影画引线 |

调试：控制台 `__rx.set(p, spinY, spinX)`、`__rx.theme('dark')`、`__rx.preset('chrome')`；URL `?p=1&theme=dark&preset=pearl`。
