# EVE Autochess · 在线热更协议（指针仓速查）

> 载荷权威：HF 桶 [liketocode789/eveautochess](https://huggingface.co/buckets/liketocode789/eveautochess)  
> Resolve：`https://huggingface.co/buckets/liketocode789/eveautochess/resolve/`  
> 本仓只镜像 `version.json` / `manifest.json` 与说明，**无 content / pck 大文件**。

来源：隔壁「Godot 永恒空壳」规划（薄壳 + 全内容热更 + Win/Android 倒 Y）。

## 流程

```
Boot → GET version.json → (可选)改写 baseUrl
     → GET manifest.json → 按 PlatformId 过滤
     → 按 sha256 跳过未变文件 → 增量下载至 user://
     → 按 mountOrder 挂载 kind=pack → entry 场景
```

「只下变化」= **manifest 条目粒度**的 sha256 跳过（非整包 `game.pck`、无包内二进制差分）。

## version.json

| 字段 | 说明 |
|------|------|
| `protocol` | 协议版本，当前 `1`；壳忽略未知字段 |
| `version` | 内容版号，建议 `YYYYMM.D.N` |
| `publishedAt` | ISO8601 |
| `baseUrl` | resolve 根 URL（末尾 `/`） |
| `entry` | 挂载后主场景，如 `res://scenes/main_menu.tscn` |
| `shellCompatibilityId` | 可选；壳协议代际，不匹配则提示重装壳 |
| `notes` | 人读说明 |

## manifest.json（多包）

```json
{
  "version": "202607.29.04",
  "files": [
    {
      "path": "packs/logic.pck",
      "sha256": "...",
      "size": 0,
      "kind": "pack",
      "mountOrder": 10
    },
    {
      "path": "packs/ui.pck",
      "sha256": "...",
      "size": 0,
      "kind": "pack",
      "mountOrder": 20
    },
    {
      "path": "packs/models.pck",
      "sha256": "...",
      "size": 0,
      "kind": "pack",
      "mountOrder": 30
    },
    {
      "path": "packs/audio.pck",
      "sha256": "...",
      "size": 0,
      "kind": "pack",
      "mountOrder": 40
    }
  ]
}
```

### 四包职责（路径互斥）

| 包 | path | mountOrder | 内容 |
|----|------|------------|------|
| logic | `packs/logic.pck` | 10 | `scripts/**` · `scenes/**` · `data/**` · `shaders/**` |
| ui | `packs/ui.pck` | 20 | `assets/ui/**` · `assets/fonts/**` |
| models | `packs/models.pck` | 30 | `assets/models/**` |
| audio | `packs/audio.pck` | 40 | `assets/audio/**` · `assets/textures/**` · `assets/skyboxes/**` |

- **已废弃**以单体 `packs/game.pck` 为唯一载荷（旧客户端首遇新 manifest 会下满四包一次；本地残留 `game.pck` **不删**，新 manifest 不再列出）。
- 改数值/脚本 → 通常只变 `logic.pck`；改舰模 → 只变 `models.pck`。

### 倒 Y 过滤

| 路径前缀 | Windows | Android |
|----------|---------|---------|
| `packs/` / `content/` / 无 platform | ✓ | ✓ |
| `platform/windows/` | ✓ | ✗ |
| `platform/android/` | ✗ | ✓ |
| 其它 `platform/*` | ✗ | ✗ |

## 壳永不更（边界）

- **只发 HF**：玩法、数值、场景、GDScript、美术、entry、分包清单变更。
- **须重打壳（EveAutoChess Releases / disv1）**：改 Boot / seed 多包逻辑、换 Godot 导出模板、权限/签名、引擎安全补丁、增删壳 Autoload。

## 材料仓

本地准备：`H:\game_dev\eveautochess-hf`  
确认「推 HF」后再 sync 到本桶；偶发把 `version.json` / `manifest.json` 同步进本 GitHub 指针仓。
