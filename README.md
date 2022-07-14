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

