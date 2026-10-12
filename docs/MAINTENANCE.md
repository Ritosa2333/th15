# TH15 维护指南

## 职责与依赖

`th15_web/cpp/game/` 拥有本作游戏对象、固定逻辑步、菜单、Replay 和资源语义。平台适配提供渲染、输入、音频与文件访问。公共算法来自本作 `third_party/eagler-common` 的 Git 锁定；主项目根 common 更新不会自动改变这里的依赖。

游戏仓库只交付 Runtime。Web 页面、资源安装、房间界面和站点组装统一由主项目维护，不恢复旧 `launcher/` 副本。

## 构建输入与输出

实际入口是 `th15_web/scripts/build-sdl-application.mjs --release`。建议从主项目使用 `npm run runtime -- th15`，显式提供 Emscripten、Python、字体和资源；本地路径不提交到源码。普通构建及目录 Runtime 通过主项目登记的 build/package 链生成，打包核对实际文件清单。

项目默认 Runtime 输出位于 dist/runtime/th15/<variant>/。编译中间产物仍由本作脚本管理，最终受管目录以暂存、验证、切换的方式更新。

## 本轮验证

普通源码构建、目录 Runtime 打包通过；真实 DATA 的 Chromium 启动、进入游戏、存档写入/重载及自托管组装通过。本轮音乐为 none。

## 练习、翻译与 Replay

练习选项、章节定位、重开和 Replay 扩展属于本作生命周期。语言静态包由 Web 准备，游戏读取已挂载资源；构建成功不证明所有文本布局和对话流程。

[Replay Verifier](../tools/replay-verifier/README.md) 使用 WASI 诊断核心。它比较已声明字段，不等同于生产浏览器 Runtime 全内存或渲染一致。本轮没有替换原版 golden。

## 维护顺序

先复现问题并确定状态所有者，修改最小职责边界，再执行对应原生、Runtime 和浏览器验证。升级 common 时记录实际 gitlink 和影响的接口。跨作品抽取需核对实现、ABI 和消费者，不因文件同名就共用。

项目[能力进度](https://github.com/YomotsuHisami/eagler-touhou/blob/main/docs/TOUHOU_PROGRESS.md)区分实现、构建和实测。[输入与 Replay](https://github.com/YomotsuHisami/eagler-touhou/blob/main/docs/playbooks/input-replay.md)、[多人联机](https://github.com/YomotsuHisami/eagler-touhou/blob/main/docs/playbooks/multiplayer.md)说明进一步验证。

## 工具与回归关注点

`th08_web/package.json` 只保留被原版对照脚本消费的工具依赖，已删除缺失 TH08 工程的命令。旧整包校验和部署目录不再作为正式入口。

退出/重开必须恢复有效混合与绘制状态；场景切换后重新建立 viewport/clip。Replay 列表保留原版编号槽与用户录像的不同排序规则；长 Replay 终止、暂停和加速按真实生命周期验证。分辨率与纹理优化分别验证图像和逻辑，不把手机模拟视口当成物理手机性能结果。
