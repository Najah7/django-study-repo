# Model

## Modelとは
Databaseとのインタフェース。<br>
Modelは、データベースのテーブルに対応する。<br>
そして、Djangoではモデル→テーブルの自動生成ができる！！<br>

## モデルで定義できるフィールド一覧
- django.db.models.fields.AutoField: 自動インクリメントされる整数フィールド
- django.db.models.fields.BigAutoField: 自動インクリメントされる64ビット整数フィールド
- django.db.models.fields.BigIntegerField: 64ビット整数フィールド
- django.db.models.fields.BinaryField: バイナリフィールド
- django.db.models.fields.BooleanField: 真偽値フィールド
- django.db.models.fields.CharField: 文字列フィールド
- django.db.models.fields.DateField: 日付フィールド
- django.db.models.fields.DateTimeField: 日時フィールド
- django.db.models.fields.DecimalField: 10進数フィールド
- django.db.models.fields.DurationField: 期間フィールド
- django.db.models.fields.EmailField: メールアドレスフィールド
- django.db.models.fields.FileField: ファイルアップロードフィールド
- django.db.models.fields.FilePathField: ファイルパスフィールド
- django.db.models.fields.FloatField: 浮動小数点数フィールド
- django.db.models.fields.ImageField: 画像フィールド
- django.db.models.fields.IntegerField: 整数フィールド
- django.db.models.fields.GenericIPAddressField: IPアドレスフィールド
- django.db.models.fields.NullBooleanField: NULLを許容する真偽値フィールド
- django.db.models.fields.PositiveIntegerField: 正の整数フィールド
- django.db.models.fields.PositiveSmallIntegerField: 正の小さい整数フィールド
- django.db.models.fields.SlugField: スラッグフィールド
- django.db.models.fields.SmallIntegerField: 小さい整数フィールド
- django.db.models.fields.TextField: テキストフィールド
- django.db.models.fields.TimeField: 時刻フィールド
- django.db.models.fields.URLField: URLフィールド
- django.db.models.fields.UUIDField: UUIDフィールド
- django.db.models.fields.related.ForeignKey: 外部キーフィールド
- django.db.models.fields.related.ManyToManyField: 多対多フィールド
- django.db.models.fields.related.OneToOneField: 一対一フィールド

## フィールドのオプション一覧
- null: TrueならNULLを許容する
- blank: Trueなら空文字を許容する
- choices: 選択肢を指定する
- db_column: データベースのカラム名を指定する
- db_index: インデックスを作成する
- db_tablespace: テーブルスペースを指定する
- default: デフォルト値を指定する
- editable: 編集可能かどうかを指定する
- error_messages: エラーメッセージを指定する
- help_text: ヘルプテキストを指定する
- primary_key: 主キーかどうかを指定する
- unique: 一意かどうかを指定する
- unique_for_date: 日付ごとに一意かどうかを指定する
- unique_for_month: 月ごとに一意かどうかを指定する
- unique_for_year: 年ごとに一意かどうかを指定する
- verbose_name: フィールドの表示名を指定する
- validators: バリデーションを指定する

## リレーションの実装方法
基本的に、参照する側にリレーションを意味するフィールドを定義する。<br>

## モデルの定義のサンプルコード

### 一対一の関係
```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=255)

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    age = models.IntegerField()
```

### 多対一の関係
```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=255)

class Article(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=255)
    body = models.TextField()
```

### 多対多の関係
```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=255)

class Group(models.Model):
    name = models.CharField(max_length=255)
    users = models.ManyToManyField(User)
```

## モデルマネージャ

## モデルマネージャとは
テーブルをクエリ操作（クエリを使って操作）するためのクエリセットAPI（QuerySet API）のインターフェース。<br>
モデルの「object」という属性に用意されている。<br>

### 代表的なモデルマネージャ
- ExampleModel.objects.all(): 全件取得
- ExampleModel.objects.filter(condition): 条件に一致するレコードを取得
- ExampleModel.objects.get(condition): 条件に一致するレコードを1件取得
- ExampleModel.objects.get().exists(): 条件に一致するレコードが存在するかどうかを取得
- ExampleModel.objects.all().count(): 全件数を取得
- ExampleModel.objects.all().order_by(field): フィールドでソート
- ExampleModel.objects.all().reverse(): 逆順にソート
- ExampleModel.objects.all().distinct(): 重複を除外
- ExampleModel.objects.all().values(field): 指定したフィールドの値を取得
- ExampleModel.objects.all().values_list(field): 指定したフィールドの値をリストで取得
- ExampleModel.objects.all().annotate(field): フィールドを追加
- ExampleModel.objects.all().aggregate(field): 集計
- ExampleModel.objects.create(data): レコードを作成
- ExampleModel.objects.all().update(field): レコードを更新
- ExampleModel.objects.first(): 最初のレコードを取得
- ExampleModel.objects.last(): 最後のレコードを取得
- ExampleModel.objects.none(): 空のクエリセットを取得
- ExampleModel.objects.select_related(): リレーション先のレコードを取得
- ExampleModel.objects.prefetch_related(): リレーション先のレコードを取得（複数）
- ExampleModel.objects.defer(field): 指定したフィールドを除外して取得
- ExampleModel.objects.only(field): 指定したフィールドのみ取得
※クエリセットは繋げてＯＫ。

#### filterの条件一覧
- =: 一致する
- __gt: より大きい
- __gte: 以上
- __lt: より小さい
- __lte: 以下
- __in: リストの中に含まれる
- __contains: 部分一致
- __icontains: 大文字小文字を区別しない部分一致
- __startswith: 前方一致
- __endswith: 後方一致
- __range: 範囲内
- __isnull: NULLかどうか
- __regex: 正規表現
- __iregex: 大文字小文字を区別しない正規表現

#### filterを使ってJOINを実装する
- `<model>_<field>`= `<value>`で指定する。<br> 
```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=255)

class Article(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=255)
    body = models.TextField()

# ユーザー名が「Taro」の記事を取得する
Article.objects.filter(user__name='Taro')
```

## モデルマネージャ以外のよく使うメソッド
- ExampleModel.save(): レコードを保存
- ExampleModel.delete(): レコードを削除

### saveとdeleteとトランザクション
saveとdeleteは呼び出されたときにデータベースに書き込まれる（オートコミット）<br>

### トランザクションを制御したい場合
django.db.transaction.atomicを使う。<br>
```python
from django.db import transaction

with transaction.atomic():
    # トランザクションを制御したい処理
```
※トランザクション中に問題が起きたら自動でロールバックされる。

# objects.create vs ExampleModelのインスタンス化
- objects.create: レコードを作成して保存する。
- ExampleModelのインスタンス化: レコードを作成するだけで保存はしない。（saveを呼び出す必要がある。）


### モデルマネージャの定義のサンプルコード
```python
from django.db import models

class ExampleModelManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_deleted=False)

class ExampleModel(models.Model):
    name = models.CharField(max_length=255)
    is_deleted = models.BooleanField(default=False)
    objects = ExampleModelManager()
```

## その他
- `bulk_create`: 複数のレコードを一度に作成する。
- `bulk_update`: 複数のレコードを一度に更新する。
- `update_or_create`: レコードが存在しない場合は作成、存在する場合は更新する。
- `get_or_create`: レコードが存在しない場合は作成、存在する場合は取得する。

