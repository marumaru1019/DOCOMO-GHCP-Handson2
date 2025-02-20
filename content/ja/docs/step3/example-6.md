---
title: ⑥ Swagger 定義の自動生成
categories: [GitHub Copilot, Engineer Usecases]
weight: 6
---

**GitHub Copilot** は、既に完成している**API実装**を解析し、**Swagger (OpenAPI) 定義**を自動生成するヒントを提供してくれます。ここでは、`UserController.java` のような **CRUD API** 実装が終わった状態で、**`#file`** 指定を使って「Swagger定義書を作ってほしい」と依頼する例を紹介します。

---

### :pen: 例題

以下に示す **`UserController.java`** は、Spring Boot + Springdoc 前提の簡易 CRUD 実装です。

```java
package com.example;

import org.springframework.web.bind.annotation.*;
import java.util.*;

@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    private Map<Integer, String> users = new HashMap<>();
    private int idCounter = 1;

    @GetMapping
    public List<String> getAllUsers() {
        return new ArrayList<>(users.values());
    }

    @PostMapping
    public Map<String, Object> createUser(@RequestBody Map<String, String> request) {
        String name = request.get("name");
        users.put(idCounter, name);
        Map<String, Object> response = new HashMap<>();
        response.put("id", idCounter);
        response.put("name", name);
        idCounter++;
        return response;
    }

    @GetMapping("/{id}")
    public Map<String, Object> getUser(@PathVariable int id) {
        Map<String, Object> response = new HashMap<>();
        response.put("id", id);
        response.put("name", users.getOrDefault(id, "Not found"));
        return response;
    }

    @PutMapping("/{id}")
    public Map<String, Object> updateUser(@PathVariable int id, @RequestBody Map<String, String> request) {
        String name = request.get("name");
        users.put(id, name);
        Map<String, Object> response = new HashMap<>();
        response.put("id", id);
        response.put("name", name);
        return response;
    }

    @DeleteMapping("/{id}")
    public Map<String, String> deleteUser(@PathVariable int id) {
        users.remove(id);
        return Collections.singletonMap("status", "ok");
    }
}
```

#### プロンプト例

```text
#file:UserController.java

この実装に基づいて、OpenAPIのYAML形式でAPI定義を書いてください。
タイトル: User Service, バージョン: 1.0.0
```

**Copilot** は**Controller** 内の **`/api/v1/users`** などの各エンドポイントを解析し、OpenAPI/Swagger YAMLを提案してくれます。

---

### :robot: 出力例

```yaml
openapi: 3.0.3
info:
  title: User Service
  version: "1.0.0"
paths:
  /api/v1/users:
    get:
      summary: Get all users
      operationId: getAllUsers
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: array
                items:
                  type: string
    post:
      summary: Create a user
      operationId: createUser
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  type: string
      responses:
        '200':
          description: Created
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                  name:
                    type: string
  /api/v1/users/{id}:
    get:
      summary: Get a user by ID
      operationId: getUser
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                  name:
                    type: string
    put:
      summary: Update a user
      operationId: updateUser
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  type: string
      responses:
        '200':
          description: Updated
    delete:
      summary: Delete a user
      operationId: deleteUser
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Deleted
```

Copilot は**GET /api/v1/users**, **POST /api/v1/users**, **GET/PUT/DELETE /api/v1/users/{id}** に対応したスキーマやパラメータを自動的に推測し、**OpenAPI形式**で書き出します。

---

### :memo: 練習

1. **パラメータ追加**
   - コードを修正して `@GetMapping(params = "region")` 等を増やした後、「再度OpenAPI定義を生成して」と頼む
2. **OpenAPI Generator**
   - Copilot が出力した YAML を使って**クライアントSDK**を作る → Copilotに「生成した YAML でクライアントSDKを試したい」と話しかける
3. **大規模API**
   - 複数Controllerをまとめて `#file:` で指定し「全API分のOpenAPI書いて」と依頼 → どの程度正確に生成されるか試す

---

## まとめ

- **API実装** (`UserController.java`など) を **`#file`** として読み込ませ、「Swagger 定義を作って」と伝える → **OpenAPI YAML**をCopilotが自動生成
- **HTTPメソッドや@PathVariable**、**RequestBody** の型などを**自動解析** → **paths** や**schema**セクションが埋まる
- 生成された YAML は**swagger-ui** や**OpenAPI Generator** でさらに活用可能

こうして**API実装→Swagger定義**の流れも、Copilotが**コード解析**をして**自動ドキュメント**化に役立つため、**開発効率**が大幅に向上します。