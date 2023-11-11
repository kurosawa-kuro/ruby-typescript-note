# Ruby on Rails playwrightガイド

npm install --save-dev jest
npm install --save-dev playwright
npm install --save-dev @playwright/test


```
const { chromium } = require('@playwright/test');

// Jestのテストブロックを定義します。
test('Indexページに"New"というテキストが表示されている', async () => {
    let browser;
    let page;
    try {
        // ブラウザインスタンスを起動します。
        browser = await chromium.launch({ headless: true });

        // 新しいページを開きます。
        page = await browser.newPage();

        // localhost:3000にナビゲートします。
        await page.goto('http://localhost:3000/microposts');

        // ページ内に特定の文字列"New"が含まれていることを確認します。
        const isVisible = await page.isVisible('text="New"');

        // Jestのアサーションを使用して、文字列が見えることを確認します。
        expect(isVisible).toBeTruthy();

    } catch (error) {
        console.error('テストに失敗しました:', error);
        if (page) {
            await page.screenshot({ path: 'test-failure.png' });
        }
        throw error; // Jestが失敗として認識できるように例外を再スローします。
    } finally {
        // エラーが発生してもしなくても、必ずブラウザを閉じます。
        if (browser) {
            await browser.close();
        }
    }
});
```