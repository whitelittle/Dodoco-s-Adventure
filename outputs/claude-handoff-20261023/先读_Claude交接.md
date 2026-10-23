# 嘟嘟可大冒险完整交接（2026-10-23）

用户要求：包括素材在内完整移交，已有图片在本地拟合成Lua数据，供Claude续作。本次为本地文件交付；没有Git提交、推送或向Claude账号发送文件。没有新增美术或改动main。完整包保留工程内原图、参考、阶段稿、失效历史、脚本、构建入口、工具、探针、GIA、存档、迁移备份和验证资料；文件清单逐项记录哈希。依赖目录、代理配置及凭据不作为工程交付，排除项见清单。

## 从哪里开始

1. 解压完整ZIP，工程根目录是“嘟嘟可大冒险”。首先读根目录AGENTS.md、outputs/project-guide/续接摘要.md。本文件覆盖旧交接文档的入口说明；旧文档中的史莱姆、火斧等素材状态已多次被后续指令覆盖，不能直接采用其旧状态。
2. 看outputs/monster-redraw-v2/20261009队列收尾.md、manifest.json和各资产当前verification.json。最新30只新候选均有移动素材，另保留冰/雷法师，共32种。20261009的0814探针实际只轮换30只新候选，不能说包含32只。它们不是全部获用户美术认可。
3. 需要素材时先读本目录fitted-assets-index.json，再载入all_fitted_assets.lua。完整工程里的PNG、工程JSON、RGBA、矩形JSON和独立Lua仍保留，方便追溯、编辑和重建。
4. 后续在outputs/local-project/development或独立work/outputs修改验证；用户确认后才同步main。不要创建Git仓库。

## 单Lua素材接口

```lua
local assets = dofile('all_fitted_assets.lua')
local clip = assets.currentMonsterClips[1]
local frame = clip.frames[1]
for _, rect in ipairs(frame.rects) do
  local x,y,w,h,r,g,b,a = table.unpack(rect)
  -- 将矩形送入项目图片池；这里的坐标是原画布像素坐标。
end
local retainedIce = assets.fittedExportsByPath[assets.retainedMagePaths[1]]
```

这是离线可载入的纯数据表，不是直接挂到千星的启动脚本。字符串是UTF-8字节十进制转义，文件ASCII、无BOM、LF；矩形RGBA为0–255，帧列表从1开始。渲染必须继续使用原工程的anchor、displayScale、offsetY、镜像和帧事件语义，不能把原画布坐标直接当世界坐标。Lua数据本身不会创建图片池、实现AI、战斗或分批加载。一次性把全素材灌进千星会增加加载和内存负担，应离线选取需要的角色/帧并按现有分批框架构建。

currentMonsterManifest与currentMonsterClips：当前30只移动候选，与原manifest/library逐项一致。retainedMagePaths：从历史阵容里仅保留冰/雷法师两条路径；不能因此复活整个旧阵容。fittedExportsByPath：按原始路径收录所有已有帧/矩形导出，以及主角、组件和环境数据；包含对照档与失效历史，仅用于追溯，不代表当前推荐。原图、参考和废稿不当作新的有效资产去重拟合。需要的新美术仍先给用户看样图。

本次fit-verification.json记录30只当前角色的逐帧边界、无重叠检查及有RGBA原始文件时的重建一致性；没有新做模拟器或真机验证。Lua执行检查结果以该报告为准，不把序列化成功写成真机可用。

## 版本、玩法与测试边界

确定主版本：outputs/map-v1.0/gtp_20260929_1450_v1.0_黑屏过图_原碰撞恢复.gia。25房随机地图、黑屏分批、加载弹跳史莱姆；旧capped与map5x5-procedural稳定回退保留。左右门洞-44<y<28，x=±348；边界碰撞大于可见墙石是角色身体余量设计，不能机械按石材收紧。没有当前5000图元硬限制，也不要求实例绝对不增长。

基础战斗与火法/射手战斗在outputs/combat-demo-v0.1和outputs/combat-demo-pyro-archer-v0.1，独立于主地图。20261001_0811基础启动修订包获用户真机通过反馈，性能未量化；火法/射手1201真机包仍待反馈。不要推广为所有新素材/多怪真机通过。

火深渊法师、岩盔王、雷丘射手是保留通过素材，路径分别在outputs/pyro-abyss-mage与outputs/hilichurl-electro-stonehide。其他状态查素材审核状态.json及续接摘要。火斧攻击v4/行走v3后续获通过，早期降色及动作失效稿不能复活。木盾/守卫采用修复步序的后续版本，初始脚位和左右腿身份要固定。20261009队列完成后已停生图，本次不擅自扩展技能或继续队列。

## 构建与外部依赖

Node.js >=22。development/README.md有npm run build、npm test、npm run build:probe、npm run test:probe。设置SIMULATOR_ROOT指向已安装依赖的miliastra-beyond-simulator源码。原机器路径为C:/Users/Cheng/Documents/deepseek-harness/default-workspace/miliastra-beyond-simulator；它在本工程之外，完整ZIP不冒充包含它。在新机器需要单独取得此依赖，替换work脚本中硬编码的file:///旧机器路径。素材重拟合入口work/monster-redraw-fit.mjs；当前阵容探针work/monster-redraw-probe-build.mjs；本次打包入口work/build-claude-handoff.py（python运行）。

静态/动画工具outputs/static-material-tool，启动工具.ps1，默认4190；模拟器默认4187。组件探针outputs/component-probe及work/component-probe-*，动作探针outputs/animation-probe及其源码、GIA、存档、说明、验证均须长期保留。旧清理备份不得整包恢复覆盖当前工程。新GIA交付命名gtp_日期_时间_版本说明.gia。

## 交接后第一项工作

先按用户下一条具体玩法需求继续，复用已拟合数据；不要把“已打包”解释为“已把32种怪物整合进主地图”。如要整合，先评估玩法、视觉、负载、现有约束与操作体验，说明具体冲突和替代方案。真机帧率、启动耗时、多怪运行和未确认美术均不能靠本次打包获得保证。
