# Release preparation notes — external companion

论文：**Auditing Operating Context in Spectral Anomaly Detection for Multichannel Streetlight Monitoring under Distribution Shift**。

归档版本：**2026-10-29 冻结的17卷归档候选版**，由 `ARCHIVE_VOLUMES.csv` 和 `SHA256SUMS.txt` 中的逐卷校验值唯一标识；尚无公开发布版本号。本外置说明编写于2026-10-30，不改变任何冻结卷或既有校验值，也不新增合并ZIP。

用途：提供历史与修订评价、正式合成实验的代码、配置、split manifests、随机种子、最终checkpoint及保存输出，支持材料审查和后续复现准备。**本仓库为私有仓库，归档附件存放于草稿 Release；未公开发布，无 DOI。仓库地址：https://github.com/zszhang724/streetlight-context-audit 。**

## 下载组合与文件位置

| 组合 | 文件 | 覆盖范围 |
|---|---|---|
| 必需核心组合（9卷） | `code_and_docs.tar.gz` ＋ `large_core_001.tar.gz` 至 `large_core_008.tar.gz` | A/B/C主要结果的材料覆盖及相关开发选参记录 |
| 可选补充（8卷） | `large_supplement_001.tar.gz` 至 `large_supplement_008.tar.gz` | 已选合成控制、敏感性及部分G2材料；不是额外的C正式运行 |

17卷共5,108,285,022字节。科学文件清单覆盖24,599个逻辑文件、19,718份去重载荷；说明和工具另由卷内清单覆盖。各卷是独立的tar.gz，不能用二进制拼接方式合并。

将下载文件按以下关系摆放：本说明、`SHA256SUMS.txt`、`ARCHIVE_VOLUMES.csv`、`ARCHIVE_VALIDATION.json`、`LOGICAL_VOLUME_MAP.csv` 位于同一父目录，17卷或所选组合放在其 `packages/` 子目录。若下载服务把文件平铺，请自行按此布局放置；保留原文件名。校验列表使用 `packages/` 相对路径。

## 校验、解压与恢复逻辑目录

以下命令供接收者下载后执行，**本次仅编写说明，没有执行这些命令或重复验证已通过卷**。环境为 Bash、GNU `sha256sum`、`tar`、Python 3.10或以上；从上述下载父目录运行。`unpacked`、`work_core` 和 `work_all` 应使用尚不存在的目录，避免混合版本。遇到任何错误应停止。

### 核心组合

```bash
set -euo pipefail

# 只校验已下载的代码卷和8个核心卷；不要求下载补充卷。
awk '$2 == "packages/code_and_docs.tar.gz" || $2 ~ /^packages\/large_core_/ {print}' SHA256SUMS.txt | sha256sum -c -

mkdir unpacked
tar -xzf packages/code_and_docs.tar.gz -C unpacked

# 沿用包内工具，检查成员路径、大小及内容哈希。
python3 unpacked/public_release/tools/verify_archive.py \
  packages/code_and_docs.tar.gz packages/large_core_{001..008}.tar.gz

for volume in packages/large_core_{001..008}.tar.gz; do
  tar -xzf "$volume" -C unpacked
done

# 根据MANIFEST恢复所有逻辑路径，包括只物理保存一次的内容别名。
python3 unpacked/public_release/tools/materialize.py \
  --destination ./work_core --scope core
```

`unpacked/public_release/` 是去重后的归档布局，不能假定其中每个原始逻辑路径都已存在。`materialize.py` 使用包内 `MANIFEST.csv` 逐项找到载荷、核验哈希并恢复到新目录；不会运行训练、推理或评价。`LOGICAL_VOLUME_MAP.csv` 提供逻辑文件→载荷→归档卷的外置对应关系。

### 加入可选补充卷

在核心组合已解压后，如需全部已选合成控制材料：

```bash
set -euo pipefail

awk '$2 ~ /^packages\/large_supplement_/ {print}' SHA256SUMS.txt | sha256sum -c -

python3 unpacked/public_release/tools/verify_archive.py \
  packages/large_supplement_{001..008}.tar.gz

for volume in packages/large_supplement_{001..008}.tar.gz; do
  tar -xzf "$volume" -C unpacked
done

python3 unpacked/public_release/tools/materialize.py \
  --destination ./work_all --scope all
```

### 路径迁移

默认恢复保留公开副本中的路径标记。仅在计划后续科学执行时，阅读包内 `REPRODUCIBILITY.md` 并准备独立工作目录。现有工具支持 `--bind-paths`、`--training-python`、`--generation-python`；解释器路径必须由使用者提供，不能假设打包环境就是原实验环境。

`--bind-paths` 只在新的工作副本中替换相应文本标记，不修改归档，不自动消除 `__UNAVAILABLE_EXTERNAL_PATH__`，也不会重写历史哈希保护。迁移后的文本可能不再满足原worker的冻结哈希条件；必须明确记录并审查新的路径与清单绑定，不能直接绕过保护。上述恢复工具已提供并经过静态解析，其完整工作副本执行及迁移后科学运行不在既有验收声明内。

## A/B/C关系

- **A：历史结果。** 使用生成种子1337的数据，共享TIME-validation超参数选择。Table 4主比较为CATCH、Raw context、WC-CATCH、TranAD，共60份最终checkpoint。
- **B：Full aligned修订评价。** 对历史WC-CATCH的15份权重进行canonical完整覆盖再评价；其权重来自A，不是新的独立开发训练。30个canonical CATCH/Raw伴随对照也复用A权重。R3 FULL_ALIGNED记录复用相应B输出。共享权重只存一份，并保留映射。
- **C：独立的G0/G1正式合成实验。** 按协议在开发数据上选参，270次正式运行及270份最终checkpoint。其生成实例、开发选择和汇总单位不同于A/B，不能把B直接称为C。

详细对应关系见包内 `RESULTS_MAP.csv`、`FROZEN_SEEDS.csv`、`CHECKPOINT_COVERAGE.csv`。A/B的共享TIME-validation选择限制不会因重新评价或公开归档而消失。

## 已验证与未验证事项

既有 `ARCHIVE_VALIDATION.json` 记录17卷全部通过、失败0；验证包括实际解压、成员路径安全、内容哈希与清单一致及逻辑文件/载荷/卷的完整映射。本说明沿用这些已完成记录，没有再次读取全部载荷。

**归档完整性通过不等于干净环境端到端复现通过。** 本归档准备没有重训、推理、生成数据或重算指标，也未验证全新环境安装、GPU跨设备确定性或迁移后原worker执行。已有历史验证报告只说明当时的实验，不构成本轮新增复现。

## 排除内容与限制

- 真实数据扩展研究载荷保持排除；本包不能单独复现那些真实数据结论。
- 15份补充IsolationForest joblib checkpoint因安全元数据检查未完成而排除；历史 `tail.py` 非独立可解析源码片段保持排除。它们不减少A/B/C主结果checkpoint覆盖。
- 不包含全部中间训练快照、弃用搜索/开发权重、旧ZIP、原始会话、私有调度日志等；补充G2仅有24次已完成运行，不计入C的270次。
- 部分历史环境和源码绑定记录不完整，包括TranAD Time seed42 stdout等缺失；当前环境信息不能填补这些空缺。
- 私人绝对路径与主机标识已在公开副本中清理，改动记录见 `PUBLIC_COPY_CHANGES.csv`。纳入的checkpoint与科学数值载荷保留原字节；路径迁移仍需单独处理。

## 许可状态：公开前仍需作者处理

自有代码、生成数据、模型权重及文档尚未选择发布许可证；需作者确认权利及各类材料的授权范围。本说明不授予任何新许可证，不能把代码许可自动套用于数据、权重或第三方内容。

现有说明记录Open-Meteo天气数据的CC BY 4.0来源及署名要求；第三方通知包括TranAD的BSD-3-Clause、Anomaly-Transformer和Time-Series-Library的MIT、TSB-AD的Apache-2.0。须保留实际适用的版权、许可和署名，并确认其与所收录改编文件的对应关系。CATCH相关内容及部分其他改编基线仍需明确来源与再分发授权；没有找到LICENSE不代表允许公开。

详情沿用包内 `DATA_AND_LICENSES.md` 与 `ARCHIVE_STATUS.md`。这些未决事项需在实际公开上传前解决；“已打包”不构成发布许可或公开状态。

## Table 4 / Table 8来源更正

包内旧README的A行仍称Table 4仅由恢复到的七表参考稿支持、未恢复送审包。该句的最新说明应以本外置记录为准，冻结卷本身未修改：

**本地助手已从作者提供的二审提交ZIP确认表号、标签和对应数值；本服务器未独立读取该ZIP。** 作者提供的本地文件为 `C:/Users/ASUS/Downloads/Revised manuscript.zip`。据本地助手确认，ZIP内 `main.tex` 共10张表：Table 4为 `tab:main_results`，Scenario WC-CATCH AP为0.167；Table 8为 `tab:shuffle_mechanism`，Full aligned Scenario AP为0.1844。服务器不声称核验该ZIP的哈希或完整生成链。现有A/B实验来源映射继续保留。

## 上传文件清单（7项；仅供后续获准发布时使用）

1. `README_RELEASE.md`（本外置说明）
2. `packages/code_and_docs.tar.gz`
3. `packages/large_core_001.tar.gz` 至 `packages/large_core_008.tar.gz`（8卷）
4. `packages/large_supplement_001.tar.gz` 至 `packages/large_supplement_008.tar.gz`（可选8卷）
5. `SHA256SUMS.txt`
6. `ARCHIVE_VOLUMES.csv`、`ARCHIVE_VALIDATION.json`
7. `LOGICAL_VOLUME_MAP.csv`

卷内已含MANIFEST、RESULTS_MAP、许可/环境/复现说明及恢复工具，无需另造一份合并包。保留校验清单中的17卷记录；仅下载核心组合的接收者使用上述筛选命令。实际发布前解决许可待办，发布后才填写真实仓库地址和DOI。本次仅进行私有仓库与草稿 Release 上传，未公开发布或创建 DOI。


## 私有上传说明（2026-10-31）

此首页以原 README_RELEASE.md 为基础，仅更新私有存储状态；原 README_RELEASE.md 作为冻结说明快照保留在仓库和 Release 附件中。附件 v0.1.0-archive-candidate 为草稿，不是公开发布。许可待办仍须在公开前解决。上传后的远端名称、字节数与可用 digest 核对，不等于重新执行本地完整性验证或端到端复现。
