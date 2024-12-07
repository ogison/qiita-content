---
title: PythonでBacklogAPIを使ってみた
tags:
  - Python
  - Backlog
  - backlogapi
private: false
updated_at: '2024-05-28T00:48:47+09:00'
id: 378c49e426c616c18007
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
最近、PythonでBacklogAPIを使用して簡単なアプリを作りました。
次にBacklog APIを使用するときや、他の人も使用できるようにメモ程度にまとめます。

# APIの使用方法
Backlog APIは2種類の認証と認可の方法が存在しています。
+ Backlogユーザ毎に発行されたAPIKeyを使用する方法
+ OAuth2.0を用いて取得したAPITokenを使用する方法

今回はAPIKeyを使用した方法を紹介します。

### API Key
APIKeyはユーザーの設定画面から取得することができます。
個人設定 -> API -> 登録
![Backlog_API.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/341d2c4d-0d1d-72ab-3686-98b7ae89c4bc.png)

登録ボタンを押下すると、APIキーを発行することができます。こちらのAPIKeyを使用して、BacklogAPIを利用することができます。

### スペース名
下の画像で囲まれているスペース名は後で使用するので控えておきます。
![Backlog_API2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/71ed19e2-4419-9a3b-3029-89c5b46cfba8.png)


# PythonでBacklogAPIを使用する
### 必要なライブラリ
基本的には、HTTPリクエストを送信してAPIを利用します。そのために必要な'requests'ライブラリを使用します。
```bash
pip install requests
```

## スクリプトの作成
まず、最初にあらかじめ用意したAPIKeyとスペース名を設定します。
```python
import requests

API_KEY = 'your_api_key_here'
SPACE_NAME = 'your_subdomain_here'
```

## 使用例1：課題一覧の取得
```python
def get_projects():
  payload = {
    'apiKey' : API_KEY
  }
  response = requests.get(f'https://{SPACE_NAME}.backlog.com/api/v2/issues', payload)
  if response.status_code == 200:
      return response.json()
  else:
      print(f'Error: {response.status_code}')
      return None

projects = get_projects()
if projects:
    for project in projects:
        print(f'ID: {project["id"]}, Name: {project["summary"]}')
```

## 使用例2：課題の作成
```python
def create_issue(project_id, summary, description, issue_type_id, priority_id):
  url = f'https://{SPACE_NAME}.backlog.com/api/v2/issues'
  payload = {
    'apikey' : API_KEY,
    'projectId': project_id,
    'summary': summary,
    'description': description,
    'issueTypeId': issue_type_id,
    'priorityId': priority_id
  }
  response = requests.post(url, json=payload)
  if response.status_code == 201:
    return response.json()
  else:
    print(f'Error: {response.status_code}')
    return None

# 課題の作成例
new_issue = create_issue(
  project_id='12345',
  summary='API経由での課題作成',
  description='これはAPI経由で作成された課題です。',
  issue_type_id='1',  # 課題タイプID（Backlogの設定で確認）
  priority_id='3'    # 優先度ID（Backlogの設定で確認）
)

if new_issue:
  print(f'Created issue: {new_issue["id"]}, {new_issue["summary"]}')
```

## 使用例3：課題の更新
```python
def update_issue(issue_id, updates):
  url = f'https://{SPACE_NAME}.backlog.com/api/v2/issues/{issue_id}?apiKey={API_KEY}'
  response = requests.patch(url, json=updates)
  if response.status_code == 200:
    return response.json()
  else:
    print(f'Error: {response.status_code}')
    return None

# 課題の更新例
updated_issue = update_issue(
  issue_id=74043178,
  updates={
    'summary': '更新された課題のタイトル',
    'description': 'この課題はAPIを使用して更新されました。'
  }
)

if updated_issue:
  print(f'Updated issue: {updated_issue["id"]}, {updated_issue["summary"]}')
```

# まとめ
Backlog APIを使用して、効率化しましょう。
