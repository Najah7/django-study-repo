# View

## Viewの役割
リクエスト（django.http.request.HttpRequestオブジェクト）を受け取り、レスポンス（django.http.response.HttpResponseオブジェクト）を返すまでの処理を行うこと。

### 具体的には
- バリデーション
- ビジネスロジック(モデルへのメッセージ送信。テンプレートへの変数の設定＆メッセージ送信)

## Viewの2種類の書き方
- 関数ベースのビュー:Viewを関数として作成。0から自分で実装系。
- クラスベースのビュー：Viewをクラスとして作成。便利なクラスはDjangoが提供してくれている。なので、楽はできる。（※しかし、OPPを知らないとデバッグが面倒な場合もあり。）
※使い方が一番違う。
- 関数ベース：関数を呼び出すのみ
- クラスベース：インスタンス化してから呼び出す（ExampleView.as_view()を使う）

### 関数ベースのサンプルコード
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

### クラスベースのサンプルコード
```python
from django.http import HttpResponse
from django.views import View

class MyView(View):
    def get(self, request):
        # <view logic>
        return HttpResponse('result')
```

## Djangoが提供してくれるクラスベースのビュー一覧（普通のDjangoのビュー（テンプレートを使うことが前提））
- django.views.generic.base.View：ベースのビュークラス。他のビューの基底クラスとして使用する。ほぼ0から実装したいときに便利。（その分デバッグも楽）
    - django.views.generic.base.TemplateView：テンプレートを使うビュー。テンプレートをレンダリングしてレスポンスを返す。
    - django.views.generic.base.RedirectView：リダイレクトを行うビュー。リダイレクト先をget_redirect_url()メソッドで取得する。
    - django.views.generic.list.ListView：オブジェクトのリストを表示するビュー。テンプレートにオブジェクトのリストを渡す。
    - django.views.generic.detail.DetailView：オブジェクトの詳細を表示するビュー。テンプレートにオブジェクトを渡す。
    - django.views.generic.edit.FormView：フォームを表示するビュー。テンプレートにフォームを渡す。
    - django.views.generic.edit.CreateView：オブジェクトを作成するビュー。テンプレートにフォームを渡す。
    - django.views.generic.edit.UpdateView：オブジェクトを更新するビュー。テンプレートにフォームを渡す。
    - django.views.generic.edit.DeleteView：オブジェクトを削除するビュー。テンプレートにオブジェクトを渡す。
    - django.views.generic.dates.ArchiveIndexView：オブジェクトのリストを日付でグループ化して表示するビュー。テンプレートにオブジェクトのリストを渡す。
    - django.contrib.auth.views.LoginView：ログイン画面を表示するビュー。テンプレートにフォームを渡す。

## Djangoが提供してくれるクラスベースのビュー一覧（RESTfulなAPIを作るときのビュー）
- rest_framework.views.APIView:RESTfulなAPIを作るときのビュー。テンプレートを使わない。
    - rest_framework.generics.GenericAPIView: 汎用的なビューのベースのView。テンプレートを使わない。
        - rest_framework.generics.CreateAPIView: POSTメソッドでオブジェクトを作成するビュー。
        - rest_framework.generics.RetrieveAPIView: GETメソッドでオブジェクトの詳細を表示するビュー。
        - rest_framework.generics.UpdateAPIView: PUTメソッドでオブジェクトを更新するビュー。
        - rest_framework.generics.DestroyAPIView: DELETEメソッドでオブジェクトを削除するビュー。
        - rest_framework.generics.ListAPIView: GETメソッドでオブジェクトのリストを表示するビュー。
        - rest_framework.generics.ListCreateAPIView: GETメソッドでオブジェクトのリストを表示するビュー。POSTメソッドでオブジェクトを作成するビュー。
        - rest_framework.generics.RetrieveUpdateAPIView: GETメソッドでオブジェクトの詳細を表示するビュー。PUTメソッドでオブジェクトを更新するビュー。
        - rest_framework.generics.RetrieveDestroyAPIView: GETメソッドでオブジェクトの詳細を表示するビュー。DELETEメソッドでオブジェクトを削除するビュー。
        - rest_framework.generics.RetrieveUpdateDestroyAPIView: GETメソッドでオブジェクトの詳細を表示するビュー。PUTメソッドでオブジェクトを更新するビュー。DELETEメソッドでオブジェクトを削除するビュー。
    - rest_frmework.viewsets.ModelViewSet: 汎用的なビューのベースのView。モデルを指定するだけでCRUD機能がすぐに実装できる。（しかし、間に特殊な処理を挟んだりする場合、逆に作成が面倒になる可能性もある。）
    - rest_framework.viewsets.ReadOnlyModelViewSet: 破壊的なメソッド（POST, PUT, DELETE）を除いたCRUD機能がすぐに実装できる。

### rest_framework.generics.GenericAPIViewのミックスイン（これを継承することでメソッドごとの機能を選んでViewを作れる）
        - rest_framework.mixins.RetrieveModelMixin: GETメソッドでオブジェクトの詳細を表示するビュー。
        - rest_framework.mixins.UpdateModelMixin: PUTメソッドでオブジェクトを更新するビュー。
        - rest_framework.mixins.DestroyModelMixin: DELETEメソッドでオブジェクトを削除するビュー。
        - rest_framework.mixins.ListModelMixin: GETメソッドでオブジェクトのリストを表示するビュー。



※すべて大本はdjango.views.generic.base.View