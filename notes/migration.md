# マイグレーションファイル

## マイグレーションとは
マイグレーションファイルの内容をDBに反映すること

## マイグレーションファイルとは
DBのテーブルの構造を定義するファイル

## マイグレーションファイルの作成
Djangoでは、モデルを作成したら、マイグレーションファイルがコマンド一つで自動生成される。<br>

## マイグレーションファイルの作成コマンド

```bash
$ python manage.py makemigrations <app_name> <app_name> ...
```

## マイグレーションファイルの確認
マイグレーションファイルは、アプリケーションのmigrationsディレクトリに格納される。<br>
マイグレーションファイルは、アプリケーションごとに作成される。<br>

## マイグレーションファイルの実行

```bash
$ python manage.py migrate <app_name> <app_name> ...
```

