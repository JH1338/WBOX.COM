# WBOX 游戏中心

WBOX 的静态游戏站，首发作品 **WBox Minecraft C++ 版**。首页是游戏卡片墙，Minecraft 只是第一张卡，后面加游戏补一张卡片即可。纯 HTML，直接丢 GitHub Pages 就能跑。

## 目录

```
index.html        整站（样式和逻辑全部内联）
assets/hero.png   游戏卡片封面
data/users.json   所有 WBOX 账号数据（单个 JSON）
.nojekyll         关闭 Jekyll，让 Pages 原样发布
```

## 部署

1. 新建仓库，把本目录全部文件推上去（保留 `assets/`、`data/`）。
2. Settings → Pages → Deploy from a branch，分支 `main`、目录 `/ (root)`。
3. 仓库设成 **Public**，否则访客读不到账号数据。

## 加一张游戏卡片

在 `index.html` 的 `#games` 里复制一个 `.game` 块，改封面图、标题、`meta` 版本行，按钮指向新下载链接即可：

```html
<div class="game reveal">
  <div class="cover"><img src="assets/xxx.png" alt=""><span class="tag">已上架</span></div>
  <div class="body">
    <h3>游戏名</h3>
    <div class="meta">v1.0 · Windows</div>
    <div class="desc">一句话介绍</div>
    <div class="act"><a class="btn primary sm" href="下载链接">下载</a></div>
  </div>
</div>
```

占位的 `.slot` 块就是「待上架」坑位，上线时删掉、换成 `.game` 即可。

## 下载

`index.html` 里的 `const DL_URL` 是 Minecraft C++ 版的网盘地址（Buzzheavier），点下载会先弹 3 秒倒计时遮罩再跳转。换链接改这一处，另外页脚还有一条同样的链接。

## WBOX 账号

- 一个账号通用 WBOX 全部项目；注册/登录全在前端，密码用 `SHA-256 + 站点盐 + 600 轮迭代` 存哈希。
- 每台浏览器在 `localStorage` 留副本；管理员在「⚙ 数据同步设置」里填 `owner/repo`、分支、`data/users.json` 和一个 GitHub 令牌，就能拉取 / 合并上传 / 导出 / 导入。
- 数据结构：

```json
{
  "version": 1,
  "updatedAt": 1750000000000,
  "users": [
    {
      "username": "pangpang",
      "nickname": "胖胖2023",
      "passHash": "…",
      "role": "member",
      "createdAt": 1750000000000,
      "lastLogin": 1750000000000,
      "downloads": 3,
      "updatedAt": 1750000000000
    }
  ]
}
```

## 一句实话

静态站点没有服务端：令牌只存在管理员那台浏览器里，不能写进代码或公开仓库。所以别人在自己设备注册的账号，需要管理员点一次「合并并上传」才会进仓库 JSON；想让所有访客注册即写入，只能加一层 Cloudflare Worker / 云函数持有令牌，那不是 GitHub Pages 能干的事。
