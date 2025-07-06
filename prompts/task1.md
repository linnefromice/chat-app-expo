@docs/upstream/requirements.md
@docs/upstream/requirements-api-pattern.md

要件定義のためのドキュメントを作成したいです。
requirements-api-pattern.md に、 このアプリの要件である習慣化アプリのAPI設計が記載されています
これをネイティブアプリに閉じる形に修正してください

モデリングはローカルデータのモデル構造として扱い、 API設計はサービスクラスのインターフェースとして実装してください。
また、本アプリは React Native, Expoを使用してクロスプラットフォーム開発をするため、シンプルなUI、OS側のネイティブなUIを使うことを心がけてください。
また React Native の特徴をできる限り活かし、可能な限り Native API を使用しない方向です

テスト方針について
ロジックに対するUTのテストコードは記述します
ビジュアルについてはスナップショットテストを導入します
