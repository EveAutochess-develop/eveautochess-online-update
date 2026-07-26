# eveautochess-online-update · 仓库6（热更指向）

GitHub **仅导向**仓。热更载荷在 Hugging Face Bucket，**不进本 git 仓**。

对齐隔壁「永恒空壳」讨论：[薄壳进安装包，玩法全走远端 PCK](https://github.com/orgs/EveAutochess-develop/repositories) · 协议对齐 TopDog `version.json` → `manifest` → 文件。

| 用途 | URL |
|------|-----|
| HF 桶（content / packs） | https://huggingface.co/buckets/liketocode789/eveautochess |
| 客户端 resolve 基址 | https://huggingface.co/buckets/liketocode789/eveautochess/resolve/ |
| 本仓 version 指针（镜像） | [version.json](version.json) |
| Org | https://github.com/orgs/EveAutochess-develop/repositories |

## 壳 vs 内容（冻结边界）

| 层 | 进安装包（仓库3 发布物） | 热更（本仓指向的 HF） |
|----|-------------------------|----------------------|
| Runtime 壳 | 引擎、Boot、下载器、版本门、最小 UI | **原则上永不因玩法更壳** |
| Content / PCK | 可无或仅基线 | 场景、资源、绝大部分 GDScript、`packs/*.pck` |
| 协议 | 壳读 `version.json` + `manifest` | 内容演进；壳忽略未知 JSON 字段 |

硬规则：启动必须能走到「检查更新 → 挂载 pack → 进主游戏」，否则热更失败会黑屏。

## 协议 v1（双端倒 Y）

1. `GET {resolve}/version.json`
2. 若有 `baseUrl` → 后续请求改用该基址
3. `GET {resolve}/manifest.json`
4. 按平台过滤后增量下载（sha256）；落盘 `user://`
5. 按 `mountOrder` `load_resource_pack`，再 `change_scene(entry)`

共享路径（双端都拉）：`packs/**`、`content/**`、无 `platform/` 前缀。  
分端：`platform/windows/**`、`platform/android/**`。

版号建议：`YYYYMM.D.N`（例 `202607.26.1`）。

## 与其它仓

| 仓 | 关系 |
|----|------|
| `eveautochess-hf` | **材料准备**（本地 staging）；确认后推 HF 桶 |
| 本仓 `eveautochess-online-update` | **公开指针**；偶发同步 `version.json` / README，不堆二进制 |
| `eveautochess-release` | 壳安装包；协议未破不重打 |
| `eveautochess-dev` | Godot 开发；内容导出进 hf staging |
| `eveautochess-design` | 规则手册权威 |

## 发布

```powershell
# 材料仓准备后推 HF（须用户确认「推 HF」）
# 在 eveautochess-hf 使用 hf buckets sync …

# 刷新本 GitHub 指针（小文件）
git add version.json README.md PROTOCOL.md
git commit -m "sync hotupdate pointer"
git push origin main
```

默认：**HF 为权威**；GitHub 只给人看与可选镜像 `version.json`。
