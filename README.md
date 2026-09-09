# metal-lily-hero

个人主页首屏：左侧是一束用 three.js 参数化建模的金属百合（弯曲花瓣、下垂的喇叭筒、弯曲花苞），随页面滚动从茎开始生长、展叶、开花、蝴蝶飞入；右侧 `ruxinxue`（全息渐变字）与 Duke ECE · 2nd year / SDE · Agent development · Full-stack 逐行浮现。花朵旁有四条带引线的技术栈标注。

## 运行

单文件，无构建。three.js 通过 jsdelivr importmap 加载，字体来自 Google Fonts，需要联网。

```bash
# 直接双击 index.html，或者起个本地服务
python3 -m http.server 8791 --directory .
# 然后打开 http://127.0.0.1:8791/index.html
```

## 交互

| 操作 | 效果 |
| --- | --- |
| 滚动 | 驱动生长时间线（页面高 320vh，画布固定） |
| 鼠标拖拽画布 | 360° 旋转花束，背面与正面同样完整（全部薄面 DoubleSide） |
| 顶栏 `pearl / holo / chrome` | 材质预设：珍珠白金属 / 全息虹彩（彩色面板 PMREM 环境）/ 镜面铬。默认 holo，记在 localStorage |
| 顶栏 ☀ / ☾ | 亮 / 暗主题，默认亮色，记在 localStorage |
| 鼠标移动 | 轻微视差 |

## 结构

所有代码都在 `index.html`：

| 区块 | 说明 |
| --- | --- |
| `petalFn(open, opts)` | 百合花瓣参数曲面。`open=0` 花苞、`1` 盛开，两态作为 morph target 混合实现开花；`curl` 瓣尖后卷、`twist` 扭转、`sweep` 侧弯、`ruf` 波浪边 |
| `bendGeometry / bakeBend` | 把整朵花（花瓣、花萼、雄蕊）沿轴线弯成弧，默认朝重力方向，`bendAz` 可指定 |
| `curvy(pts, amp, seed)` | 给茎的控制点加 S 形摆动 |
| `leafFn / smallPetalFn` | 披针形叶片、五瓣小花 |
| `tubeGeo` + `growMaterial` | 收细的茎管 + 注入 `uGrow` 的材质，片元 `vAlong > uGrow` 时 discard，实现"长出来" |
| `STEMS` | 花束布局：每根茎的控制点、生长区间 `t: [t0, t1]`、挂件（leaf / lily / bud / cluster / minibud / tendril） |
| `BUTTERFLIES` | 两只蝴蝶 |
| `PRESETS` / `acidEnv()` | 三种材质预设；holo 用一组高亮彩色面板生成的 PMREM 环境 |
| `TAGS` | 花朵旁的技术栈标注：3D 锚点 + 屏幕偏移，每帧投影并画引线 |
| `.intro [data-at][data-len]` | 右侧文字的出现进度窗口，文案直接改 HTML |

## 调试

- 控制台 `__rx.set(p, spinY, spinX)`：跳到任意生长进度 / 旋转并同步渲染一帧；`__rx.theme('dark')`、`__rx.preset('chrome')`。
- URL 参数 `?p=1&theme=dark&preset=holo&sy=3.14` 打开即跳到该状态。
