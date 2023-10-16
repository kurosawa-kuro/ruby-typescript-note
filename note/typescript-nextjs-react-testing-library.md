# TypeScript・Next.js・React Testing Library セットアップガイド

Next.jsでの詳細なテストの手法やベストプラクティスについては、[公式ドキュメント](https://nextjs-ja-translation-docs.vercel.app/docs/testing)を参照してください。

## 1. クイックスタート

Jestを利用したNext.jsの新しいプロジェクトを手軽に開始するには、以下のコマンドを実行します。

```bash
npx create-next-app@latest --example with-jest nextjs-jest-app
```

## 2. Jestのカスタム設定（Rust コンパイラ使用）

まず、Jestと関連するTesting Libraryのパッケージをインストールします。

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
```

次に、Jestの設定を行います：

```javascript
// jest.config.js
const nextJest = require('next/jest');

const createJestConfig = nextJest({
  // Next.js設定および.envファイルへのパス
  dir: './',
});

// 追加のJest設定
const customJestConfig = {
  // テストの前に実行されるセットアップ
  // setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  
  // TypeScriptでルートディレクトリがbaseUrlとして設定されている場合のalias
  moduleDirectories: ['node_modules', '<rootDir>/'],
  testEnvironment: 'jest-environment-jsdom',
};

// 非同期でNext.jsの設定を読み込むようにエクスポート
module.exports = createJestConfig(customJestConfig);
```

## 3. テストコードサンプル

```
Topページのテスト
```

## 4. モックサンプル

Topページでの非同期通信処理をMSWを用いてモック化し、それを基にテストを行います。

