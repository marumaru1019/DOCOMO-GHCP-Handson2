---
title: ⑥ 不要なコンテキストの排除
categories: [GitHub Copilot, Prompt Engineering]
weight: 6
---

**GitHub Copilot** は、**開いているファイル**や**チャット履歴**の文脈を参照してコード提案を行います。もしその**文脈が膨大で不要**な情報にあふれていると、Copilot が誤ったコードを生成するリスクが高まります。ここでは、**API 定義が多い Markdown** から **一部の特殊な API** だけ使いたい例を取り上げ、**不要なコンテキストをどう排除するか**を解説。さらに、**履歴を整理**する手段も紹介します。

---

### :pen: 例題

#### シナリオ
- **既存ファイルの参照**: `api_definitions.md` に大量のAPIが書かれている  
- **特定のなユーザーAPIの使用** (`GET /api/v1/users?region={region}&includeInactive=true`) だけ使いたい  
- **新ファイルの作成**: `UserApiClient.java` を作り、そのAPIを呼び出すロジックを実装する

##### `api_definitions.md` (一部抜粋)

```markdown
## 1. GET /api/v1/users?region={region}&includeInactive=true
- Description: Retrieve users in a specific region.
- Query params:
  - region: string
  - includeInactive: boolean
- Response: JSON array of user objects
```

**実際**にはさらに多くのAPIが含まれているが、ここでは**このAPI** だけ利用したい。

#### プロンプト例

```text
"UserApiClient.java" をJava17で記述してください:
- メソッド: fetchUsersByRegion(String region, boolean includeInactive)
- #selection  の API を呼び出す
- 戻り値: List<User>
  - User: {id, name, region, active}
```

この際、**#selection** には `GET /api/v1/users?region={region}&includeInactive=true` の定義を含めてください。

### :robot: 出力例

```java
public class UserApiClient {
    
    public List<User> fetchUsersByRegion(String region, boolean includeInactive) throws IOException {
        String urlStr = String.format("https://example.com/api/v1/users?region=%s&includeInactive=%b",
                                       region, includeInactive);

        // HTTP GET
        URL url = new URL(urlStr);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");

        // Parse response JSON
        try (InputStream in = conn.getInputStream()) {
            ObjectMapper mapper = new ObjectMapper();
            User[] usersArray = mapper.readValue(in, User[].class);
            return Arrays.asList(usersArray);
        }
    }
}
```

**不要API** (`/api/v1/products`, `/api/v1/orders` など) は**文脈**に含めず、**一部**だけ抜粋したため、Copilot は**望み通りのAPI**を使うコードを生成しやすくなります。

---

### :bulb: 履歴の整理
チャットの履歴に一貫性がないと、Copilot が**誤った文脈**を読み込んでしまうリスクがあります。そのため、**不要なコンテキスト**を排除し、**必要な部分**だけ提示することが重要です。
新しく話題を始める際は、**新規チャット**を開始するか、**/clear** コマンドで履歴をリセットすることで、Copilot が**正確なコード**を生成しやすくなります。

#### Tips
チャットの上部にある **巻き戻しマーク** をクリックすると、**過去のスレッド**を見直すことができます。これにより、一時的に新しいスレッドを開始したけど、前の文脈に戻りたいという際に便利です。

**VS Code:**
![Image](https://github.com/user-attachments/assets/4ff69a21-97cf-4d42-b8fa-31054f86d1c1)

**JetBrains IDE:**
![Image](https://github.com/user-attachments/assets/7702b582-6695-40e9-b615-75d2ece79c8c)

### :memo: 履歴を整理する方法

1. **/clear コマンド**  
   - Copilot チャットに `/clear` を入力すると新規チャットが開始されます
2. **巻き戻しマークで整理**  
   - 過去メッセージを削除して不要な文脈を消す 
   - **巻き戻しマーク**をクリックして、過去のスレッドを見直す

不要なコードや会話ログを Copilot が参照しないようにすると、**誤推測**が減り、**意図通り**の提案が得やすくなります。

---

## まとめ

- **大量のAPI定義などがある場合**、必要な部分だけ**抜粋**または**#file**指定 → Copilot が**誤った文脈**を読み込まないようにする  
- **履歴が膨大**だと、Copilot が混乱するリスク上昇 → **/clear** 等で履歴を整理  
- このように**不要なコンテキスト**を排除し、**必要部分**だけ提示すれば、Copilot が**精度の高いコード**を提示してくれます