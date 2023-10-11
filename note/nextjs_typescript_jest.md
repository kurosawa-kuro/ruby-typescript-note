# Next.js Typescript Jest のセットアップガイド

## パターン1: クイックスタート

Next.js アプリを Jest でテストするためのクイックセットアップを行います。

```bash
npx create-next-app@latest --example with-jest nextjs_typescript_jest
```

## パターン2: 手動で Jest を設定

### 1. Next.js アプリの作成

```bash
npx create-next-app@latest nextjs_typescript_jest
```

### 2. 必要なパッケージのインストール

```bash
npm install --save-dev jest jest-environment-jsdom ts-jest @testing-library/react @testing-library/jest-dom @testing-library/user-event eslint-plugin-jest-dom eslint-plugin-testing-library
```

### 3. Jestの設定

`jest.config.js` をプロジェクトのルートディレクトリに作成し、以下の内容を追加します：

```javascript
const nextJest = require('next/jest');

const createJestConfig = nextJest({
    dir: './',
});

/** @type {import('jest').Config} */
const config = {
    setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
    testEnvironment: 'jest-environment-jsdom',
    preset: 'ts-jest',
};

module.exports = createJestConfig(config);
```

### 4. Jestのセットアップファイル

`jest.setup.js` をプロジェクトのルートディレクトリに作成し、以下の内容を追加します：

```javascript
require('@testing-library/jest-dom');
```

### 5. ESLintの設定

`.eslintrc.json` をプロジェクトのルートディレクトリに作成し、以下の内容を追加します：

```json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:testing-library/react",
    "plugin:jest-dom/recommended"
  ]
}
```

### 6. テストの書き方

`__tests__/Home.test.tsx` を作成し、以下のようにテストを書きます：

```typescript
import '@testing-library/jest-dom';
import { render, screen } from '@testing-library/react'
import Home from '@/app/page'

describe('Home', () => {
    it('should have Docs text', () => {
        render(<Home />) // ARRANGE 

        const myElem = screen.getByText('Docs') // ACT 

        expect(myElem).toBeInTheDocument() // ASSERT
    })

    it('should contain the text "information"', () => {
        render(<Home />) // ARRANGE 

        const myElem = screen.getByText(/information/i) // ACT 

        expect(myElem).toBeInTheDocument() // ASSERT
    })

    it('should have a heading', () => {
        render(<Home />) // ARRANGE 

        const myElem = screen.getByRole('heading', {
            name: 'Learn ->'
        }) // ACT 

        expect(myElem).toBeInTheDocument() // ASSERT
    })
})
```

