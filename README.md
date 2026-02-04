# AgentTeamCooperate

> Internal agent collaboration for LAN-only tasks with safe model switching (OpenAI / local). 此專案用於內網的多代理協作，提供模型切換、安全規範與外掛化介面。

## 功能概述 (Overview)
- **多代理任務協作**：支援在局域網內的任務分派與協作。
- **模型切換**：可手動切換雲端模型（OpenAI/Gemini 等）或本地模型（Ollama，如 llama3.2:3b、llama3.1:8b、qwen2:7b）。
- **Skills/外掛機制**：以外掛方式擴展能力（範例可參考 rlm 等開源專案）。
- **內網限定**：僅允許內網 IP 存取；對外只接受指定通訊管道（如 WhatsApp 綁定號碼）。

## 安全規範 (Safety Policy)
- **禁止濫用／攻擊**：如外掛或程式有危害他人設備、服務的行為，一律拒絕並撤銷其訪問權限。
- **禁止洩漏機敏資訊**：不得在代碼或設定中暴露私有金鑰、Token、私有 API，若檢測到疑似洩漏，必須立即停用該外掛／帳號並輪替憑證。
- **最小權限**：外掛與 Agent 必須使用最小必要權限的憑證；共享資源時應用隔離策略。
- **審核與封鎖**：若發現外掛或提交存在危害或洩漏風險，專案維護者可立即封鎖該來源並移除相關提交。
- **內網可見**：預設服務僅對內網可見；若要暴露到外網，需明確的 allowlist 與額外驗證層。

English:
- If a plugin or linked program appears to harm others’ devices/services, or leaks private data/API keys, its access is revoked immediately.
- Do not commit secrets. If a leak is suspected, disable the plugin/account and rotate credentials.
- Use least-privilege credentials and isolation.
- Maintainers may block/rollback any unsafe contribution.
- Default LAN-only; any WAN exposure must be explicitly allowlisted and authenticated.

## 責任聲明 (Disclaimer)
- 若將此專案開源程式碼或修改後用於非自用場景，必須遵守相關法律規範，不得用於非法／商業詐欺／造假／危害研究／侵害他人、企業、國家或他人 AI、造成損失等行為，相關責任由使用者自負。
- 如原始碼被修改成有洩漏私有資訊等功能，因創建者無法逐一篩選或負責，所有後果由使用者自負。
- 若用於非自用，必須取得相關國家／公司企業／社群／團體／人士的合法授權；未經授權不得使用。
- 使用者賦予 AI 過大權力而造成的社會影響、跨國法律責任等，創建者概不負責，使用者需依所在地及受影響國家法規自負責任。

English:
- If you use this project (original or modified) beyond personal use, you must comply with applicable laws and may not use it for illegal/fraudulent/harmful purposes; all liabilities rest with the user.
- If the code is modified to leak private data, the creators cannot vet every change; users bear all consequences.
- For non-personal use, obtain proper authorization from relevant countries/companies/organizations/individuals; unlicensed use is prohibited.
- Any societal impact or cross-border legal responsibility from granting excessive power to AI is solely the user’s responsibility; creators are not liable.

## 開源使用規範 (Open Source Usage Policy)
- 本專案開源程式碼僅供合規、自用或取得合法授權後使用。
- 不得用於違法、詐欺、造假、危害研究、侵犯他人／企業／國家權益或造成損失的用途。
- 若外掛或修改版本涉及洩漏私有資料、API、金鑰或對他人造成危害，使用者須自行承擔全部責任，創建者不負責。
- 若用於非自用，須取得相關國家、公司、社群、團體或個人的合法授權；未經授權不得使用。
- 任何因賦予 AI 過大權力造成的社會或跨國法律影響，均由使用者依各地法規自負責任。

English:
- This open-source code is for lawful personal use or use with proper authorization only.
- Do not use it for illegal, fraudulent, harmful, or rights-infringing purposes.
- If plugins or modifications leak private data/API keys or cause harm, users bear full responsibility; creators are not liable.
- For non-personal use, obtain authorization from relevant countries/companies/communities/groups/individuals; unlicensed use is prohibited.
- Any societal or cross-border legal impacts from granting excessive power to AI are the user’s responsibility.

## 結構 (Structure)
```
AgentTeamCooperate/
├─ README.md
├─ LICENSE
├─ server/              # API / UI 層，提供模型切換與任務控制
├─ agents/              # 代理與協作邏輯
├─ skills/              # 外掛化模組（例如 rlm 介面、模型封裝）
└─ scripts/             # 部署與工具
```

## Skills / 插件接口
- 建議以獨立資料夾放置，每個 skill 包含：
  - `skill.yml`：描述名稱、版本、依賴、入口點
  - `requirements.txt` 或 `package.json`
  - `README.md` 說明如何安裝與使用
- 可將 rlm 這類開源專案以子模組或外掛形式集成，封裝為 skill。

## 開發流程 (Dev Flow)
1. Fork/Clone。
2. 建立虛擬環境並安裝依賴（依實際技術棧 Python/Node 為準）。
3. 在 `skills/` 增加新插件，遵守安全規範與最小權限。
4. 提交 PR 前請自我檢查：
   - 無敏感憑證、無攻擊性程式碼。
   - 測試通過（單元／整合）。

## 模型建議 (16GB RAM)
- 已有：`llama3.2:3b`（輕量）
- 可加：`llama3.1:8b` 或 `qwen2:7b`（中文表現佳），量化 Q4 以節省記憶體。
- >13B 不建議，容易 OOM。

## 部署 (LAN-only 範例)
- 後端綁定 `0.0.0.0` 但透過防火牆/網段限制只允許內網。
- 模型切換：後端暴露 `/models` 接口，前端提供下拉選單切換；失敗則 fallback 到本地模型。
- 通訊：僅允許指定的 WhatsApp 綁定號碼下達任務。

## 授權 (License)
MIT License（可依需求更改）。
