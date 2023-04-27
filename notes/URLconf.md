# URLディスパッチとURLconf

## URLディスパッチ
リクエストをルーティングするもの。

## URLconf
URLディスパッチのルティングを設定するもの。
URLのパターンとビューを紐付ける。

## URLConfigに関係する設定ファイルの定数
- ROOT_URLCONF: URLconfのモジュールパス

## パスコンバータとURLパターン

### パスコンバータ
Webアプリケーションのフレームワークなどで、URLのパス部分に含まれる動的な値を、コントローラーに渡すために使用される機能のことを指す。

### パスコンバータ一覧
- int: 整数
- str: 文字列（空ではない文字列）
- path: パス（空ではない文字列＋「/」）
- slug: スラッグ（半角英数字・ハイフン・アンダースコアで構築された文字列）
- uuid: UUID（8桁-4桁-4桁-4桁-12桁の16進数）
※strは省略可能。
※ほぼintしか使わない。

### パスコンバータを使ったURLパターンの設定

#### path関数
パスを設定する関数。<br>
`path(route, view, kwargs=None, name=None)`
- route: URLパターン
- view: ビュー関数
- kwargs: ビュー関数に渡すキーワード引数
- name: URLパターンに名前をつける(逆引きするときに使う)
※逆引き: URLパターンからURLを生成すること（reverse関数を用いて）

#### サンプルコード
```python
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<path:year>/', views.year_archive),
]

```

## 正規表現を使ったルーティング
正規表現を使ってURLパターンを設定することもできる。<br>
あまり使わない（設計的にわかりやすいエンドポントにすべきだから）<br>
奥の手。
`re_path(route, view, kwargs=None, name=None)`
※[正規表現チートシート](https://qiita.com/tossh/items/635aea9a529b9deb3038)

### サンプルコード
```python
from django.urls import re_path

from example.views import MyView
from example import views

urlpatterns = [
    re_path(r'^articles/2003/$', views.special_case_2003),
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
]
```

## ルーティングのポイント
- 「/」は末尾につける。（左端にはつけない）
- クエリ文字列に関するルーティングはしない。
- nameはurl.pyでuniqueであるべき。（逆引きできなくなる）

## パターンに一致しない場合
Djangoが裏側で勝手に404を返してくれる。<br>
なので、URLのエラーハンドルは気にする必要なし。

## その他のテクニック
アプリケーションごとにURLconfを分割することもできる（推奨されている）。<br>
その場合、ルートのurl.pyではinclude関数って各アプリのurl.pyにルーティングする。<br>

### サンプルコード
```python
# mysite/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls')),
]
```

```python
# articles/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('<int:year>/', views.year_archive),
    path('<int:year>/<int:month>/', views.month_archive),
    path('<int:year>/<int:month>/<int:pk>/', views.article_detail),
]
```