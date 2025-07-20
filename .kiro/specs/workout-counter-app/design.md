# 設計書

## 概要

筋トレカウントアプリケーションは、Web Speech APIを活用したTypeScript Reactアプリケーションとして実装します。音声認識、音声合成、リアルタイムUI更新を組み合わせて、ユーザーフレンドリーな筋トレアシスタントを提供します。

## アーキテクチャ

### 技術スタック
- **フロントエンド**: React 18 + TypeScript
- **音声認識**: Web Speech API (SpeechRecognition)
- **音声合成**: Web Speech API (SpeechSynthesis)
- **スタイリング**: CSS Modules または Styled Components
- **状態管理**: React Hooks (useState, useEffect, useRef)

### システム構成
```
┌─────────────────────────────────────┐
│           React App                 │
├─────────────────────────────────────┤
│  ┌─────────────┐ ┌─────────────────┐│
│  │ UI Layer    │ │ Audio Layer     ││
│  │ - Counter   │ │ - Speech Recog. ││
│  │ - Controls  │ │ - Speech Synth. ││
│  │ - Status    │ │ - Audio Utils   ││
│  └─────────────┘ └─────────────────┘│
├─────────────────────────────────────┤
│           Web Speech API            │
└─────────────────────────────────────┘
```

## コンポーネントとインターフェース

### 主要コンポーネント

#### 1. App Component
- アプリケーションのルートコンポーネント
- 全体的な状態管理とレイアウト

#### 2. WorkoutCounter Component
- メインのカウンター表示
- 現在の回数を大きく表示
- アニメーション効果付き

#### 3. VoiceControls Component
- 音声認識の開始/停止ボタン
- マイクの状態インジケーター
- リセットボタン

#### 4. StatusDisplay Component
- 音声認識の状態表示
- エラーメッセージ表示
- 最後に認識された音声の表示

### カスタムフック

#### useSpeechRecognition Hook
```typescript
interface SpeechRecognitionResult {
  transcript: string;
  confidence: number;
  isFinal: boolean;
}

interface UseSpeechRecognitionReturn {
  isListening: boolean;
  transcript: string;
  error: string | null;
  startListening: () => void;
  stopListening: () => void;
  resetTranscript: () => void;
}
```

#### useSpeechSynthesis Hook
```typescript
interface UseSpeechSynthesisReturn {
  speak: (text: string, options?: SpeechSynthesisOptions) => void;
  isSpeaking: boolean;
  cancel: () => void;
}
```

#### useWorkoutCounter Hook
```typescript
interface UseWorkoutCounterReturn {
  count: number;
  increment: () => void;
  reset: () => void;
  setCount: (count: number) => void;
}
```

## データモデル

### 音声認識設定
```typescript
interface SpeechRecognitionConfig {
  language: string; // 'ja-JP'
  continuous: boolean; // true
  interimResults: boolean; // true
  maxAlternatives: number; // 1
}
```

### 音声合成設定
```typescript
interface SpeechSynthesisConfig {
  language: string; // 'ja-JP'
  rate: number; // 0.8-1.2
  pitch: number; // 0.8-1.2
  volume: number; // 0.8-1.0
}
```

### アプリケーション状態
```typescript
interface AppState {
  count: number;
  isListening: boolean;
  isSpeaking: boolean;
  lastRecognizedNumber: number | null;
  error: string | null;
  isSupported: boolean;
}
```

## エラーハンドリング

### 音声認識エラー
- ブラウザサポートチェック
- マイクアクセス許可エラー
- ネットワークエラー
- 音声認識タイムアウト

### 音声合成エラー
- ブラウザサポートチェック
- 音声合成失敗時のフォールバック

### エラー表示戦略
- 非侵入的なエラー表示
- 自動回復機能
- ユーザーフレンドリーなメッセージ

## テスト戦略

### 単体テスト
- カスタムフックのテスト
- 数値認識ロジックのテスト
- エラーハンドリングのテスト

### 統合テスト
- コンポーネント間の連携テスト
- Web Speech API モックテスト
- ユーザーインタラクションテスト

### E2Eテスト
- 音声入力から出力までの完全フロー
- ブラウザ互換性テスト
- レスポンシブデザインテスト

## 実装詳細

### 音声認識処理フロー
1. ユーザーが開始ボタンをクリック
2. マイクアクセス許可を要求
3. 音声認識を開始
4. 音声入力を数値に変換
5. カウンターを更新
6. 音声フィードバックを再生

### 数値認識ロジック
- 英語数字の認識
- アラビア数字の認識（「1」「2」「3」等）
- 連続数字の処理
- 誤認識の除外

### UI/UX設計
- 大きく見やすいカウンター表示
- 直感的なボタン配置
- 音声認識状態の視覚的フィードバック
- モバイルファーストデザイン

### パフォーマンス最適化
- 音声認識の効率的な開始/停止
- 不要な再レンダリングの防止
- メモリリークの防止
- バッテリー使用量の最適化