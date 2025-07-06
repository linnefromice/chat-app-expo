# 習慣化アプリ要件定義（React Native/Expo版）

## 概要

本ドキュメントは、React Native/Expoを使用してクロスプラットフォーム開発する習慣化アプリの要件定義です。
アプリはローカルデータストレージを使用し、サーバーAPIに依存しない形で実装します。

## 機能要件

### 習慣管理

#### ローカルデータモデル

```typescript
interface Habit {
  id: string;                    // UUID（ローカルで生成）
  title: string;                 // 習慣の名前（例：「水を飲む」）
  description?: string;          // 習慣の説明（オプション）
  frequency: HabitFrequency;     // 頻度（DAILY, WEEKLY）
  targetDays: DayOfWeek[];       // 目標曜日（例：[月, 水, 金]）
  createdAt: Date;               // 作成日時
  updatedAt: Date;               // 更新日時
}

enum HabitFrequency {
  DAILY = 'DAILY',
  WEEKLY = 'WEEKLY'
}

enum DayOfWeek {
  MONDAY = 'MONDAY',
  TUESDAY = 'TUESDAY',
  WEDNESDAY = 'WEDNESDAY',
  THURSDAY = 'THURSDAY',
  FRIDAY = 'FRIDAY',
  SATURDAY = 'SATURDAY',
  SUNDAY = 'SUNDAY'
}
```

#### サービスクラスインターフェース

```typescript
interface HabitService {
  // 習慣の作成
  createHabit(habit: Omit<Habit, 'id' | 'createdAt' | 'updatedAt'>): Promise<Habit>;
  
  // 習慣の取得
  getHabit(id: string): Promise<Habit | null>;
  
  // 習慣一覧の取得
  listHabits(filter?: HabitFilter): Promise<Habit[]>;
  
  // 習慣の更新
  updateHabit(id: string, updates: Partial<Omit<Habit, 'id' | 'createdAt' | 'updatedAt'>>): Promise<Habit>;
  
  // 習慣の削除
  deleteHabit(id: string): Promise<void>;
}

interface HabitFilter {
  frequency?: HabitFrequency;
  targetDays?: DayOfWeek[];
}
```

### 習慣記録（チェックイン）

#### ローカルデータモデル

```typescript
interface CheckIn {
  id: string;                    // UUID（ローカルで生成）
  habitId: string;               // 関連する習慣のID
  date: Date;                    // チェックイン日付
  completed: boolean;            // 完了ステータス
  createdAt: Date;               // 作成日時
}
```

#### サービスクラスインターフェース

```typescript
interface CheckInService {
  // チェックインの記録
  createCheckIn(habitId: string, date: Date): Promise<CheckIn>;
  
  // チェックイン一覧の取得
  listCheckIns(filter: CheckInFilter): Promise<CheckIn[]>;
  
  // チェックインステータスの更新
  updateCheckInStatus(id: string, completed: boolean): Promise<CheckIn>;
  
  // チェックインの削除
  deleteCheckIn(id: string): Promise<void>;
  
  // 習慣の統計情報を取得
  getHabitStatistics(habitId: string, startDate: Date, endDate: Date): Promise<HabitStatistics>;
}

interface CheckInFilter {
  habitId?: string;
  startDate?: Date;
  endDate?: Date;
}

interface HabitStatistics {
  totalDays: number;
  completedDays: number;
  currentStreak: number;
  longestStreak: number;
  completionRate: number;
}
```

### データストレージ

#### ストレージサービスインターフェース

```typescript
interface StorageService {
  // 汎用的なCRUD操作
  save<T>(key: string, data: T): Promise<void>;
  get<T>(key: string): Promise<T | null>;
  delete(key: string): Promise<void>;
  
  // コレクション操作
  saveToCollection<T>(collectionKey: string, id: string, data: T): Promise<void>;
  getFromCollection<T>(collectionKey: string, id: string): Promise<T | null>;
  getAllFromCollection<T>(collectionKey: string): Promise<T[]>;
  deleteFromCollection(collectionKey: string, id: string): Promise<void>;
}
```

## アプリケーションアーキテクチャ

### ディレクトリ構造

```
src/
├── models/                 # データモデル定義
│   ├── Habit.ts
│   ├── CheckIn.ts
│   └── index.ts
├── services/              # ビジネスロジック層
│   ├── HabitService.ts
│   ├── CheckInService.ts
│   ├── StorageService.ts
│   └── index.ts
├── repositories/          # データアクセス層
│   ├── HabitRepository.ts
│   ├── CheckInRepository.ts
│   └── index.ts
├── screens/              # 画面コンポーネント
│   ├── HomeScreen.tsx
│   ├── HabitListScreen.tsx
│   ├── HabitDetailScreen.tsx
│   ├── HabitFormScreen.tsx
│   └── StatisticsScreen.tsx
├── components/           # 再利用可能なUIコンポーネント
│   ├── HabitCard.tsx
│   ├── CheckInButton.tsx
│   ├── StreakIndicator.tsx
│   └── index.ts
├── navigation/           # ナビゲーション設定
│   └── AppNavigator.tsx
├── utils/               # ユーティリティ関数
│   ├── dateHelpers.ts
│   ├── validationHelpers.ts
│   └── index.ts
└── App.tsx              # エントリーポイント
```

## UI/UX方針

### デザイン原則

1. **シンプルなUI**: React Nativeの基本コンポーネントを活用
2. **OS標準UI活用**: 各プラットフォームのネイティブUIパターンに準拠
3. **最小限のカスタマイズ**: 必要最小限のスタイリングに留める
4. **高速なパフォーマンス**: アニメーションは控えめに、レスポンシブ性を重視

### 主要画面

1. **ホーム画面**
   - 今日の習慣リスト表示
   - クイックチェックイン機能
   - 進捗サマリー表示

2. **習慣一覧画面**
   - 全習慣のリスト表示
   - 習慣の追加ボタン
   - 各習慣の簡易統計表示

3. **習慣詳細画面**
   - 習慣の詳細情報表示
   - チェックイン履歴
   - 統計情報（ストリーク、達成率）

4. **習慣編集画面**
   - フォームによる習慣情報の編集
   - 入力検証とエラー表示

## テスト方針

### ユニットテスト

- **対象**: サービス層、リポジトリ層、ユーティリティ関数
- **ツール**: Jest
- **カバレッジ目標**: 80%以上

```typescript
// テスト例
describe('HabitService', () => {
  it('should create a new habit', async () => {
    const habitData = {
      title: 'Exercise',
      frequency: HabitFrequency.DAILY,
      targetDays: [DayOfWeek.MONDAY, DayOfWeek.WEDNESDAY]
    };
    
    const habit = await habitService.createHabit(habitData);
    
    expect(habit.id).toBeDefined();
    expect(habit.title).toBe('Exercise');
  });
});
```

### スナップショットテスト

- **対象**: UIコンポーネント
- **ツール**: Jest + React Native Testing Library
- **目的**: UIの意図しない変更の検出

```typescript
// スナップショットテスト例
describe('HabitCard', () => {
  it('renders correctly', () => {
    const habit = mockHabit();
    const tree = render(<HabitCard habit={habit} />).toJSON();
    expect(tree).toMatchSnapshot();
  });
});
```

## 非機能要件

### パフォーマンス

- アプリ起動時間: 3秒以内
- 画面遷移: 即座にレスポンス
- データ保存: 非同期処理で UIをブロックしない

### データ永続性

- AsyncStorage（React Native標準）を使用
- データのバックアップ機能は将来的に検討
- マイグレーション戦略の実装

### セキュリティ

- ローカルデータの暗号化は初期バージョンでは実装しない
- 将来的なユーザー認証機能の追加を考慮した設計

### 保守性

- TypeScriptによる型安全性の確保
- ESLintによるコード品質の維持
- モジュール化による機能追加の容易性

## 実装上の注意事項

1. **React Native APIの使用を最小限に**
   - Platform-specific コードは避ける
   - ネイティブモジュールの使用は慎重に検討

2. **Expo SDKの活用**
   - Expo が提供する機能を優先的に使用
   - カスタムネイティブコードは避ける

3. **状態管理**
   - React Context APIを基本とする
   - 複雑化した場合のみ状態管理ライブラリを検討

4. **非同期処理**
   - Promise/async-awaitパターンの統一
   - エラーハンドリングの徹底
