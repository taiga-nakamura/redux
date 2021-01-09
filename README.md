# redux

## Reduxで保持すべきデータかどうかの判断基準（いずれかがYESならReduxで保持）
- そのデータはアプリケーションの他の箇所でも必要である
- そのデータを元に派生したデータを作成する必要がある
- そのデータが複数のコンポーネントで必要とされている
- そのデータを特定の時点のものに復元出来る必要がある
- そのデータをキャッシュする必要がある（Stateにデータがある場合はそれを利用し、APIで再リクエストしない）
- コンポーネントをホットリロードする際にそのデータの一貫性を保つ必要がある

## useSelector

### 特徴
- セレクタ関数の返り値（の参照）が変更されていたらuseSelectorを呼び出してるコンポーネントを再描画する
  - デフォルトでは`===`で比較してる
  - 第二引数に比較関数を渡せる
    - `==`でチェックしたい場合はreact-reduxの`shallowEqual`を渡す

### 実装時チェックポイント
- useSelectorで取得したデータ配列をArray.map()内で子コンポーネントへProps経由で渡す場合
  - 子コンポーネントで編集する可能性のあるデータは直接渡さない
    - WHY: 元のデータ配列が更新されるので、編集されていない他の子コンポーネントも全て再描画されてしまい、パフォーマンス上の問題となり得る為
  - IDのみの配列を取得しそれを子コンポーネントへ渡す
    - NOTE: セレクタ関数内で都度新しい参照の配列を生成している（`Array.map()`等で派生データを生成している）場合
      - 第二引数に`shallowEqual`を渡すか、`createSelector`でメモ化する
  - 子コンポーネント内でそのIDを元にuseSelectorでデータを取得する
  
## Redux Toolkit
  
### createSlice
- case reducer function内ではmutableなstateの編集が可能
  - 通常はNGだがcreateSlice内部でimmerライブラリを利用している為可能
- case reducer function内でstateをリターンしなくても自動でしてくれる（stateを変更するだけで良い）

### createAsyncThunk
- 第一引数: Actionのprefixとなる文字列
- 第二引数: Payload Creator Function（Promiseを返す関数もしくはasync関数で、解決時の値がAction.payloadとなる）
- 生成されたオブジェクト（仮に`foo`とする）
  - 次の3つのAction creatorを含む`foo.pending`, `foo.fulfilled`, `foo.rejected`
  
### createEntityAdapter
- いくつかのcase reducer
- getInitialState
- getSelectors
