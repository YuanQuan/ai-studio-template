# Product Research Repository

本目录用于存放 Product Agent 为产品决策获取的外部研究资料。研究资料是决策依据，不等同于已批准 PRD、产品规则或法律结论。

## 目录

- `comparables/`：同类游戏/竞品对比、功能拆解、优劣势与差异化机会。
- `market/`：市场规模、趋势、题材/玩法接受度、渠道与商业表现研究。
- `users/`：目标用户画像、需求、动机、付费/留存倾向、使用场景等研究。
- `ip_copyright/`：IP、版权、商标、素材来源与相似性等初步风险评估。
- `platforms/`：微信小游戏、抖音小游戏及其他平台的产品/市场相关研究。
- `misc/`：暂不适合归入以上类别的产品研究。

## 文档最低结构

每份正式研究文档至少包含：
1. `Research Question`：本次要回答的问题。
2. `Scope`：研究范围、地区、平台、时间窗口和排除项。
3. `Sources`：来源、URL/出处、发布日期或访问日期；区分一手/二手来源。
4. `Findings`：事实与观察，不与推断混写。
5. `Analysis`：对比、推断、机会、风险和限制。
6. `Confidence`：HIGH / MEDIUM / LOW，并说明原因。
7. `Product Implications`：可能影响哪些模块/PRD/数值/美术方向。
8. `Open Questions`：仍需验证的问题。
9. `Recommendation`：存在明确更优方向时直接给推荐；真实取舍时给多个方案。

## 版本与命名

建议路径：
`project/research/<category>/YYYY-MM-DD_<topic>_vN.md`

例如：
- `comparables/2026-09-15_idle_rpg_comparison_v1.md`
- `market/2026-09-15_wechat_minigame_acceptance_v1.md`
- `users/2026-09-15_target_user_profile_v1.md`
- `ip_copyright/2026-09-15_character_similarity_risk_v1.md`

研究允许持续 Revision；旧版本保留，不覆盖历史。

## 与 PRD 的关系

- Research 不自动改变 PRD。
- Product 如要采用研究结论，必须在对应 PRD / Decision 中显式引用。
- 已 USER_APPROVED 的 PRD 因新研究需要修改时，必须先做影响分析，再生成新 PRD 版本并重新进入用户审批。
- 下游 Agent 不得把 Research 中的推断直接当作已批准需求。

## 版权 / 合规研究边界

Product 可进行公开资料、竞品、素材来源和相似性等初步风险评估，但不得把研究文档描述为正式法律意见。存在明显版权、商标、授权、隐私或监管风险时，应标记 `HIGH_RISK` 并建议用户进行专业法律/合规确认。
