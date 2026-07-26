# EVE Autochess · 在线热更协议（指针仓速查）

> 载荷权威：HF 桶 [liketocode789/eveautochess](https://huggingface.co/buckets/liketocode789/eveautochess)  
> Resolve：`https://huggingface.co/buckets/liketocode789/eveautochess/resolve/`  
> 本仓只镜像 `version.json` 与说明，**无 content / pck**。

来源：隔壁「Godot 永恒空壳」规划（薄壳 + 全内容热更 + Win/Android 倒 Y）。

## 流程

```
Boot → GET version.json → (可选)改写 baseUrl
     → GET manifest.json → 按 PlatformId 过滤
     → 增量下载至 user:// → mountOrder 挂载 PCK → entry 场景
```

## version.json

| 字段 | 说明 |
|------|------|
| `protocol` | 协议版本，当前 `1`；壳忽略未知字段 |
| `version` | 内容版号，建议 `YYYYMM.D.N` |
| `publishedAt` | ISO8601 |
| `baseUrl` | resolve 根 URL（末尾 `/`） |
| `entry` | 挂载后主场景，如 `res://game/main.tscn` |
| `shellCompatibilityId` | 可选；壳协议代际，不匹配则提示重装壳 |
| `notes` | 人读说明 |

## manifest.json

```json
{
  "version": "202607.26.1",
  "files": [
    {
      "path": "packs/game.pck",
      "sha256": "...",
      "size": 0,
      "kind": "pack",
      "mountOrder": 10
    }
  ]
}
```

### 倒 Y 过滤

| 路径前缀 | Windows | Android |
|----------|---------|---------|
| `packs/` / `content/` / 无 platform | ✓ | ✓ |
| `platform/windows/` | ✓ | ✗ |
| `platform/android/` | ✗ | ✓ |
| 其它 `platform/*` | ✗ | ✗ |

## 壳永不更（边界）

- **只发 HF**：玩法、数值、场景、GDScript、美术、entry 变更。
- **须重打壳（EveAutoChess Releases）**：改 Boot 网络协议、换 Godot 导出模板、权限/签名、引擎安全补丁。

## 材料仓

本地准备：`H:\game_dev\eveautochess-hf`  
确认「推 HF」后再 `hf buckets sync` 到本桶；偶发把 `version.json` 同步进本 GitHub 指针仓。
