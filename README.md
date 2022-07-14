# Go_Web_Service_Gin

[Tutorial: Developing a RESTful API with Go and Gin](https://go.dev/doc/tutorial/web-service-gin)

## イントロダクション

このチュートリアルでは、GoとGin Web Framework（Gin）を使用してRESTfulWebサービスAPIを作成するための基本を紹介します。

Ginは、Webサービスを含むWebアプリケーションの構築に関連する多くのコーディングタスクを簡素化します。このチュートリアルでは、Ginを使用してリクエストをルーティングし、リクエストの詳細を取得し、応答用にJSONをマーシャリングします。

このチュートリアルでは、2つのエンドポイントを持つRESTfulAPIサーバーを構築します。サンプルプロジェクトは、ビンテージジャズレコードに関するデータのリポジトリになります。

## API 定義

```
/albums
    GET – Get a list of all albums, returned as JSON.
    POST – Add a new album from request data sent as JSON.

/albums/:id
    GET – Get an album by its ID, returning the album data as JSON.
```

## データセット

```go
// album represents data about a record album.
type album struct {
    ID     string  `json:"id"`
    Title  string  `json:"title"`
    Artist string  `json:"artist"`
    Price  float64 `json:"price"`
}

// albums slice to seed record album data.
var albums = []album{
    {ID: "1", Title: "Blue Train", Artist: "John Coltrane", Price: 56.99},
    {ID: "2", Title: "Jeru", Artist: "Gerry Mulligan", Price: 17.99},
    {ID: "3", Title: "Sarah Vaughan and Clifford Brown", Artist: "Sarah Vaughan", Price: 39.99},
}
```

`json:"artist"` のような構造体のタグは構造体のコンテンツが JSON にシリアル化されるときに、フィールドの名前を指定する。それらがないと、JSONは構造体の大文字のフィールド名を使用します。これはJSONでは一般的ではないスタイルです。

## すべてのアイテムの返却

- `gin.Context` パラメータを取る関数を記述します。この関数に任意の名前を付けることができることに注意してください。GinもGoも特定の関数名の形式を必要としません。
  - `gin.Context` : Gin の最も重要な部分です。リクエストの詳細を伝え、JSON の検証とシリアル化などを行います。（同じような名前ですが、これはGoの組み込みパッケージ `context` とは異なります。）

- `Context.IndentedJSON` で構造体をJSONにシリアル化し、応答に追加します。
  - 関数の最初の引数は、クライアントに送信する HTTP ステータスコードです。ここでは、`200 OK` を示すために `net/http` パッケージから `StatusOK` 定数を渡しています。
  - よりコンパクトな JSON を送信するため `Context.IndentedJSON` の呼び出し変えて `Context.JSON` にすることができる。実際には、インデントされたフォームはデバッグ時にはるかに扱いやすく、サイズの違いは通常小さいです。
  - （ここでは、巨大な JSON を扱うことはないので問題にはならないが、実環境では注意するべき。）

```
// WARNING: we recommend using this only for development purposes since printing pretty JSON is
// more CPU and bandwidth consuming. Use Context.JSON() instead.
```
( `Context.IndentedJSON` のコメントより)

```shell
curl http://localhost:8080/albums \
      --header "Content-Type: application/json" \
      --request "GET"
```

## 新しいアイテムの追加

Gin を使用すると、ハンドラーをHTTPのメソッドとパスの組み合わせとして関連付けることができます。このようにして、クライアントが利用しているメソッドに対して単一のパスに送信されたリクエストを個別にルーティングできます。

```shell
curl http://localhost:8080/albums \
      --include \
      --header "Content-Type: application/json" \
      --request "POST" \
      --data '{"id": "4","title": "The Modern Sound of Betty Carter","artist": "Betty Carter","price": 49.99}'
```

## 個別のアイテムの取得

URL から `id` パスパラメータを取得するために `Context.Param` を使用します。このハンドラーをパスにマップするときは、パラメーターのプレースホルダーをパスに含めます。

`/albums/:id` を `getAlbumByID` 関数に関連付けます。Gin では、パス内のアイテムの前にあるコロンは、アイテムがパスパラメーターであることを示します。

```shell
curl http://localhost:8080/albums/2
```

