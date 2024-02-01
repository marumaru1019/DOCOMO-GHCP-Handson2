---
title: ①メールを作成する
categories: [日常業務で使えるテクニック]
tags: [Few-shot, sample, docs]
---

メールの内容を指定することで、メールを作成することができます。

### :pen: 例題
次のプロンプトを、ChatGPTに入力します。

```text
私はHondaのエンジン設計部門に入社したばかりの新入社員です。
設計会議を調整するためのメールを書いてください。

#内容
## 候補日
11/14 13:00-14:00
11/28 14:00-15:00
12/04 10:00-11:00

##会議のアジェンダ
* プロジェクトの進捗
* エンジンの要件の再確認
* 技術的課題とリスクの議論

## 希望する返信
候補日はいつがいいか
アジェンダ以外でディスカッションしたい内容

##制約条件
日本語/英語どちらでも読めるように工夫してください。
```

### :robot: 出力例

Subject: 設計会議の日程調整とアジェンダ案について

皆様、

新入社員の[あなたの名前]です。HONDAのエンジン設計部門に入社し、設計会議を調整する役割を拝命しました。ご協力いただき、誠にありがとうございます。

設計会議の候補日とアジェンダ案を提案させていただきます。候補日とアジェンダ案についてのご意見をお聞かせいただければ幸いです。

**候補日:**
1. 11月14日 13:00 - 14:00
2. 11月28日 14:00 - 15:00
3. 12月04日 10:00 - 11:00

どの日が皆様にとって都合が良いでしょうか？ご返信いただければ幸いです。候補日が合わない場合、別の提案も歓迎いたします。

**会議のアジェンダ作成案:**
1. プロジェクトの進捗
2. エンジンの要件の再確認
3. 技術的課題とリスクの議論

上記アジェンダに加えてディスカッションしたいトピックがある場合、ご提案いただければと思います。設計会議の内容をより充実させるためのご意見をお待ちしております。

何かご質問やご要望がございましたら、どうぞお気軽にお知らせください。皆様との協力を楽しみにしております。

お手数をおかけしまして申し訳ございませんが、ご返信をお待ちしております。

よろしくお願い申し上げます。

[あなたの名前]
Honda エンジン設計部門新入社員

---

Subject: Scheduling of Design Meeting and Proposed Agenda

Everyone,

I am [Your Name], a new employee in Honda's Engine Design Department, and I have been tasked with coordinating our design meetings. I would like to express my gratitude for your cooperation.

I would like to propose some candidate dates for the design meeting and share a preliminary agenda. Your input on the proposed dates and agenda is highly appreciated.

**Candidate Dates:**
1. November 14th, 13:00 - 14:00
2. November 28th, 14:00 - 15:00
3. December 4th, 10:00 - 11:00

Which of these dates would be most convenient for all of you? Your response would be greatly appreciated. If these dates are not suitable, we are open to alternative suggestions.

**Proposed Agenda:**
1. Project Progress
2. Revisiting Engine Requirements
3. Discussion of Technical Challenges and Risks

If you have any additional topics or discussions you'd like to add to the agenda, please feel free to suggest. We welcome your input to make the design meeting as productive as possible.

If you have any questions or specific requests, please do not hesitate to let us know. We look forward to collaborating with all of you.

We apologize for any inconvenience and appreciate your prompt response.

Best regards,

[Your Name]
New Employee, Honda Engine Design Department

