# Sequence Diagrams

## コース作成（register）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as CustomService
    participant DB
    participant EM as EntityManager

    FE->>BE: コース作成リクエスト (CustomDTO)

    BE->>BE: 初期値設定 (delYn = N)

    loop 場所数分繰り返し
        BE->>EM: Place取得 (placeId)
        EM->>DB: SELECT Place
        DB-->>EM: Place返却
        EM-->>BE: Placeエンティティ返却

        BE->>BE: CustomPlace生成 (orderIndex設定)
    end

    BE->>BE: Customエンティティ生成および関連付け

    BE->>DB: Custom保存 (Cascade)
    DB-->>BE: 保存完了 (cno返却)

    BE-->>FE: cno返却
```

## コース詳細取得（get）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as CustomService
    participant DB
    participant WL as WishListRepository

    FE->>BE: コース詳細取得リクエスト (cno, userIdx)

    BE->>DB: Custom取得
    DB-->>BE: Custom返却

    BE->>BE: CustomDTOに変換

    BE->>BE: CustomPlaceをorderIndexでソート
    BE->>BE: Place → PlaceDTOに変換

    BE->>WL: お気に入り有無の確認
    WL->>DB: existsクエリ実行
    DB-->>WL: 結果返却
    WL-->>BE: true / false

    BE->>BE: DTOにplaces・tags・wishlist設定

    BE-->>FE: CustomDTO返却
```
## コース一覧取得
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as CustomService
    participant DB
    participant WL as WishListRepository

    FE->>BE: コース一覧リクエスト (scrollPaging, pageable)

    BE->>DB: スクロールベースのページング取得
    DB-->>BE: Customリスト返却

    loop 各Customごとに繰り返し
        BE->>BE: DTOに変換
        BE->>BE: 場所をorderIndexでソート
        BE->>BE: PlaceDTOに変換

        BE->>WL: お気に入り有無の確認
        WL->>DB: existsクエリ実行
        DB-->>WL: 結果返却
        WL-->>BE: true / false
    end

    BE->>BE: hasNext計算

    BE-->>FE: CustomResponseDTO返却
```
## コース編集（update）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as CustomService
    participant DB

    FE->>BE: コース編集リクエスト (cno, CustomDTO)

    BE->>DB: 既存Custom取得
    DB-->>BE: Custom返却

    BE->>BE: 基本情報更新 (title, description, tags)

    BE->>BE: 既存CustomPlaces削除

    loop 場所を再生成
        BE->>BE: CustomPlace生成 (orderIndex含む)
    end

    BE->>DB: 保存
    DB-->>BE: 完了

    BE-->>FE: 完了レスポンス
```
## 場所検索 (searchPlaces)
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as PlaceService
    participant DB

    FE->>BE: 場所検索リクエスト (address, name)

    alt addressなし && nameなし
        BE->>DB: 全件取得 (findAll)
    else addressとnameの両方あり
        BE->>DB: 住所＋名前で検索
    else addressのみあり
        BE->>DB: 住所で検索
    else nameのみあり
        BE->>DB: 名前で検索
    end

    DB-->>BE: Placeリスト返却

    BE->>BE: Place → PlaceDTOに変換

    BE-->>FE: PlaceDTOリスト返却
```
## お気に入り追加（addWishList）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as WishListService
    participant DB

    FE->>BE: お気に入り追加リクエスト (userIdx, customCno)

    BE->>DB: 重複有無の確認
    DB-->>BE: exists結果返却

    alt 既に存在する場合
        BE-->>FE: 例外発生（重複）
    else 存在しない場合
        BE->>BE: WishListエンティティ生成
        BE->>DB: 保存
        DB-->>BE: 保存完了
        BE-->>FE: 成功レスポンス
    end
```

## お気に入り削除（removeWishList）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as WishListService
    participant DB

    FE->>BE: お気に入り削除リクエスト (userIdx, customCno)

    BE->>DB: 対象データ削除
    DB-->>BE: 完了

    BE-->>FE: 成功レスポンス
```

## お気に入り一覧取得（getFavoritesByUser）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as WishListService
    participant DB

    FE->>BE: お気に入り一覧取得リクエスト (pageSize)

    BE->>DB: ページング取得（ソート含む）
    DB-->>BE: WishListリスト返却

    BE->>BE: DTOに変換

    BE->>BE: hasNext計算

    BE-->>FE: WishListDTO返却
```

## 人気コース取得（listPopular)
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as WishListService
    participant DB

    FE->>BE: 人気コースリクエスト (topCount)

    BE->>DB: 人気Custom ID取得（お気に入り基準）
    DB-->>BE: Custom IDリスト返却

    BE->>DB: Custom＋Placeをまとめて取得
    DB-->>BE: Customリスト返却

    BE->>BE: ID順で並び替え

    loop 各Customごとに繰り返し
        BE->>BE: PlaceをorderIndexでソート
        BE->>BE: DTOに変換
    end

    BE-->>FE: 人気コースリスト返却
```

## 人気コース全体取得（listPopularAll）
```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend
    participant BE as WishListService
    participant DB

    FE->>BE: 人気コース全体取得リクエスト (userIdx)

    BE->>DB: Customリスト取得
    DB-->>BE: Customリスト返却

    loop 各Customごとに繰り返し
        BE->>BE: PlaceをソートおよびDTOに変換

        BE->>DB: お気に入り有無の確認
        DB-->>BE: true / false
    end

    BE-->>FE: CustomDTOリスト返却
```
