---
title: ⑥仕様 → API 実装 → Swagger 定義の自動生成
categories: [GitHub Copilot, Engineer Usecases]
weight: 6
---

**Copilot** は、**完成したコード**を読み取り、**Swagger定義**を自動生成するヒントも提供できます。ここでは、**API実装**が終わった状態で、**実装ファイルを `#file` で渡し**「その実装をもとに Swagger で API 定義書を作ってください」と依頼するイメージを紹介します。

---

## 1. API の実装が終わった後のシナリオ

たとえば、`UserController.java` による **CRUD API** が完成し、**Spring Boot + Springdoc** を前提としているとします。

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

--- 

## 2. Swagger定義を自動生成する – Copilot へのプロンプト例

以下のように**チャットビュー**で `UserController.java` を**#file**指定し、  
「**この実装をもとにSwagger定義を作って**」と伝えると、Copilot が**OpenAPI/Swagger YAML**を提案してくれます。

```text
#UserController.java の実装をもとにswaggerでAPIの定義書を作成して
```

---

## :robot: 出力例（イメージ）

Copilot が**OpenAPI/Swagger YAML**を提案:

```yaml
openapi: 3.0.3
info:
  title: User Service
  version: 1.0.0
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
          description: OK
```

Copilot は、**UserController** の各エンドポイント (`GET/POST/PUT/DELETE`) を解析し、それぞれに対応するOpenAPI定義を**YAML**形式で提案してくれます。  

---


## 練習

1. **追加のパラメータ**  
   - たとえば「GET /api/v1/users?region=xx」などのQueryParamを実装に追記し、Copilot に再度「swagger定義を生成して」と頼む  
2. **OpenAPI Generator** で逆に**クライアントSDK**を作ってみる  
   - Copilot が出力したYAMLを**OpenAPI Generator**に渡して、Java/Kotlin/TypeScriptクライアントを試作  

---

## まとめ

- **API 実装ファイル** (例: `UserController.java`) を **`#file`** 指定 → 「Swagger定義を作って」と依頼  
- Copilot が**エンドポイント構造**や**HTTPメソッド**等を解析し、**OpenAPI/Swagger YAML**の雛形を提案  
- さらに**swagger-ui** や **OpenAPI Generator**で **APIドキュメント**や**SDK**を自動生成すれば、**開発効率**が大幅に向上  

このように、**コードベース**から**Swaggerドキュメント**まで、**Copilot** が連携して自動生成をサポートしてくれるのが大きなメリットです。