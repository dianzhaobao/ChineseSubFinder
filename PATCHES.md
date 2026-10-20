# CSF-strm 补丁说明

相对上游 ChineseSubFinder/ChineseSubFinder 的改动（全部为最小补丁）：

1. **默认视频扩展名加入 `.strm`**
   - `pkg/types/common/constvalue.go`：新增常量 `VideoExtStrm = ".strm"`。
   - `pkg/util.go`：`IsWantedVideoExtDef` 默认列表加入 `.strm`。
   - 目的：目录扫描把 `.strm` 文件当作视频文件（strm 媒体库，如 onestrm 生成）。

2. **小文件过滤对 `.strm` 放行**
   - `pkg/filter/filter.go`：`SkipFileInfo` 对 `.strm` 文件直接返回不跳过。
   - 目的：上游为软链接问题 #558 加入了「文件 <1000 字节则跳过」的规则；而 `.strm` 是文本播放列表文件，天生只有一两百字节，导致 strm 媒体库里的电影永远扫不进视频列表。

构建：GitHub Actions（`.github/workflows/build-strm.yml`）编译前端 + 后端二进制，
再用 `Dockerfile.strm` 基于官方镜像仅替换 `/usr/local/bin/chinesesubfinder`，推送到 GHCR。

已评估但未做改动的点：媒体信息（时长等）走 IMDB ID 的网页 API 查询，不需要探测本地
`.strm` 文件；字幕时间轴修复（ffmpeg/VAD）对 `.strm` 无意义，保持关闭即可。
