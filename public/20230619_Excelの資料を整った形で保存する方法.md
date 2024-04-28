---
title: Excelの資料を整った形で保存する方法
tags:
  - Excel
  - tips
  - ExcelVBA
  - マクロ
private: false
updated_at: '2023-06-19T23:53:47+09:00'
id: f60ade34aaaf71c5e03f
organization_url_name: null
slide: false
ignorePublish: false
---
# Excelの資料をきちんと整理
 社会人になって、シートがたくさん多いExcelのファイルを扱うことが多くないですか？そんなExcelファイルをきれいな形で保存する方法をまとめました！

具体的には、Excelに以下の操作を行います。
- すべてのシートのカーソルの位置をA1に移動させる
- すべてのシートの表示率を100%にする
- 1枚目のシートに移動させる

# Excelのマクロ（VBA）
　マクロとは、エクセルの操作を自動化する機能であり、中身はVBA言語で記述されます。VBAは「Visual Basic for Applications」の略でExcelで使われるプログラム言語です。

このVBAを使ってExcelのファイルをきれいに保存します！

# 手順
## マクロ作成
1. 「Alt + F11」を押してVBEを起動させる 
　VBE（VisualBasicEditor）はVBAを記述するエディタのようなものです。
    ![1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/3409a65d-409a-f3b2-bad9-1c45c06b90e5.png)

2. メニューバー「挿入」→「標準モジュール」をクリック
    ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/10bbaa5d-0eb5-8fc3-6724-fdaa77932fc7.png)

3. 作成したModuleに下記のコードを張り付ける
     ```Cursor_A1()
    Sub Cursor_A1()
    ' 画面更新を無効にすることで処理過程を見ない（目がチカチカしなくなる）
    Application.ScreenUpdating = False
    
    Dim ws As Worksheet
    Dim TargetCheck As String
       ' 各シートを順番に見ていく
       For Each ws In Worksheets
           ' カーソルをA1に移動
           TargetCheck = TargetCheck & ws.Name
           ' 表示しているシートを対象
           If ws.Visible = True Then
               ws.Select
               Range("A1").Select
               ' 表示位置を左上端に移動
               ActiveWindow.ScrollColumn = 1
               ActiveWindow.ScrollRow = 1
               ' 表示倍率を１００％に戻す
               ActiveWindow.Zoom = 100
           End If
       
       Next ws
    
       ' １枚目のシートに移動
       Sheets(1).Select
       ' 画面更新を再開させておく
       Application.ScreenUpdating = True
       ' 完了メッセージを表示
       MsgBox "処理が完了しました"
    
    End Sub

     ```
    ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/f093e42e-9bd2-83f4-363d-873dea582311.png)

## マクロ実行
- VBAで実行またはF5キーを押して「Cursor_A1」を実行させる
- Excel上で表示→マクロからでも実行できる
    ![2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2603981/730b7649-7e68-c638-dd09-6b60e4620e61.png)


## エラーが出る場合
　すべてのシートの整理はできますが、1枚目のシートに移動しないときがあります。そのときは、1枚目のシートが非表示になっている確率が高いです。

Excel上でホーム→書式→非表示/表示→シートの再表示から確認可能です！

# 最後に
　仕事で作成した資料を見やすいように保存しましょう！
