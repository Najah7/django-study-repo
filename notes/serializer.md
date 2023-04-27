# シリアライザ

## シリアライザとは
- シリアライズとデシリアライズを行うためのもの
- シリアライズやデシリアライズするにあたってのバリデーション

### シリアライズ
オブジェクトをバイト列(xmlやjsonが主)に変換すること

#### シリアライズの流れ
instance -> to_representation() -> data -> is_valid() -> validated_data

### デシリアライズ
バイト列（xmlやjsonが主）をオブジェクトに変換すること

#### デシリアライズの流れ
data -> is_valid() -> validated_data -> save() -> instance

## validationの仕組み
- OK: data -> is_valid() -> _validated_data -> validated_data(アクセス可能な属性)
- NG: data -> is_valid() -> _errors -> errors(アクセス可能な属性)

## シリアライザの中身で呼び出されるメソッド（is_valid()を実行したら呼び出されるメソッド）
- run_validation(): バリデーションを開始するメソッド(シリアライザの)
    - run_validators(): バリデーションを行うメソッド(フィールド単体の。入力フィールドの数だけ)
    - `validate_<field>()`：任意のフィールドのバリデーションを行うメソッド
    - run_validators(): バリデーションを行うメソッド。Metaクラスのvalidatorsに定義しているバリデーションを行う
    - validate()：複数のフィールド間のバリデーションを行うメソッド

## バリデーションを追加する方法
- Metaクラスのextra_kwargsにvalidatorsを追加する
- `validate_<field>()`メソッドを追加
- Metaクラスのvalidatorsに追加
- validate()メソッドを追加

## シリアライザで大事な属性とメソッド
- validated_data: バリデーション済みのデータを格納する属性(dict型)
- instance: シリアライズ対象のオブジェクトを格納する属性
- data: シリアライズしたデータを格納する属性(dict型)
- errors: エラー情報を格納する属性(dict型)


シリアライザで大事なメソッド
- is_valid(): バリデーションを行うメソッド
- save(): シリアライズしたデータを保存するメソッド
- create(): シリアライズしたデータを元にオブジェクトを作成するメソッド
- update(): シリアライズしたデータを元にオブジェクトを更新するメソッド
- to_representation(): シリアライズする際に呼び出されるメソッド

## シリアライザクラス
- rest_framework.serializers.Serializer: シリアライザクラスの基底クラス
- rest_framework.serializers.ModelSerializer: モデルをシリアライズするためのクラス: モデルの属性を利用して、バリデーションなどをしてくれるシリアライザクラス
- rest_framework.serializers.ListSerializer: 複数のオブジェクトをシリアライズするためのクラス

## シリアライザの引数
- data: シリアライズするデータ。デシリアライズする場合は、バイト列。シリアライザするときは、None
- instance: シリアライズ対象のオブジェクト。デシリアライズする場合は、None、シリアライザするときは、モデルオブジェクト
- many: 複数のオブジェクトをシリアライズするかどうか

## フィールドオプションの指定法
- フィールドクラスを使う
- フィールドオプションを指定する（extra_kwards）

## シリアライザのフィールドクラス
- rest_framework.serializers.Field: シリアライザのフィールドクラスの基底クラス
- rest_framework.serializers.CharField: 文字列をシリアライズするためのクラス
- rest_framework.serializers.IntegerField: 整数をシリアライズするためのクラス
- rest_framework.serializers.FloatField: 浮動小数点数をシリアライズするためのクラス
- rest_framework.serializers.BooleanField: 真偽値をシリアライズするためのクラス
- rest_framework.serializers.DateTimeField: 日時をシリアライズするためのクラス
- rest_framework.serializers.ChoiceField: 選択肢をシリアライズするためのクラス
- rest_framework.serializers.ListField: リストをシリアライズするためのクラス
- rest_framework.serializers.DictField: 辞書をシリアライズするためのクラス
- rest_framework.serializers.SerializerMethodField: シリアライザのメソッドをフィールドとして利用するためのクラス（`get_<field_name>`の戻り値をフィールド名に）

## シリアライザのフィールドのオプション
- read_only: 読み取り専用にするかどうか
- write_only: 書き込み専用にするかどうか
- required: 必須かどうか
- allow_null: nullを許可するかどうか
- default: デフォルト値
- validators: バリデーションを行う関数のリスト
- error_messages: エラーメッセージの辞書
- label: ラベル
- help_text: ヘルプテキスト
- initial: 初期値
- source: モデルの属性名
- style: レンダリングする際のスタイル
- allow_blank: 空文字を許可するかどうか
- trim_whitespace: 先頭と末尾の空白を削除するかどうか
- min_length: 最小文字数
- max_length: 最大文字数
- min_value: 最小値
- max_value: 最大値
- child: 子フィールド
- source: モデルの属性名


## サンプルコード

### Serializerクラスのサンプルコード
```python
from rest_framework import serializers
from .models import Sample

class SampleSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField(max_length=100)
    age = serializers.IntegerField()

    def validate_age(self, value):
        if value < 0:
            raise serializers.ValidationError('ageは0以上の値にしてください')
        return value

    def validate(self, data):
        if data['name'] == 'hoge' and data['age'] < 20:
            raise serializers.ValidationError('hogeは20歳以上にしてください')
        return data

    def create(self, validated_data):
        return Sample.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.name = validated_data.get('name', instance.name)
        instance.age = validated_data.get('age', instance.age)
        instance.save()
        return instance
```

### ModelSerializerクラスのサンプルコード
```python
from rest_framework import serializers

class SampleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Sample
        fields = ('id', 'name', 'age')
        extra_kwargs = {
            'id': {'read_only': True},
        }
        exclude = ('created_at', 'updated_at')
```

### ListSerializerクラスのサンプルコード
```python
from rest_framework import serializers

class SampleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Sample
        fields = ('id', 'name', 'age')
        extra_kwargs = {
            'id': {'read_only': True},
        }

class SampleListSerializer(serializers.ListSerializer):
    child = SampleSerializer()
```

### MethodFieldクラスのサンプルコード
```python
from rest_framework import serializers

class SampleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Sample
        fields = ('id', 'name', 'age')
        extra_kwargs = {
            'id': {'read_only': True},
        }

    def get_name(self, obj):
        return obj.name.upper()
```


