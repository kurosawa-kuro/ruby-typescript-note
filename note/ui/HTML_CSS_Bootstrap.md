# Bootstrap

## Bootstrapの動作確認用

```
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bootstrap Cheatsheet</title>

    <!-- External Stylesheets and Libraries -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
    <link rel="stylesheet" href="./style.css">
</head>

<body>
    <div class="container light-blue-background p-3 mt-4">
        <h1 class="mt-4 text-center">Bootstrap Cheatsheet</h1>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">リスト表示</h2>
            <ul class="list-unstyled mb-4">
                <li>項目1</li>
                <li>項目2</li>
                <li>項目3</li>
            </ul>
            <ul class="list-inline mb-4">
                <li class="list-inline-item">項目1</li>
                <li class="list-inline-item">項目2</li>
                <li class="list-inline-item">項目3</li>
            </ul>
            <div class="list-group mb-4">
                <a href="#" class="list-group-item list-group-item-action">項目1</a>
                <a href="#" class="list-group-item list-group-item-action">項目2</a>
                <a href="#" class="list-group-item list-group-item-action">項目3</a>
            </div>
        </section>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">テキストスタイル</h2>
            <div class="mb-1 text-primary">Hello World</div>
            <div class="mb-1 text-success">Hello World</div>
            <div class="mb-1 text-info">Hello World</div>
            <div class="mb-1 text-warning">Hello World</div>
            <div class="mb-1 text-danger">Hello World</div>
            <div class="mb-1 text-muted">Hello World</div>
        </section>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">ボタンとリンク</h2>
            <button class="btn btn-primary mb-2">Button</button>
            <a href="#" class="btn btn-primary mb-2">Link</a>
            <input type="submit" class="btn btn-primary mb-2" value="Submit">
        </section>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">イベント登録フォーム</h2>
            <form action="">
                <div class="mb-3">
                    <label for="eventName">イベント名</label>
                    <input type="text" id="eventName" class="form-control" placeholder="イベント名を入力してください">
                </div>

                <div class="mb-3">
                    <label for="organizer">主催者名</label>
                    <input type="text" id="organizer" class="form-control" placeholder="主催者名を入力してください">
                </div>

                <div class="mb-3">
                    <label for="email">連絡先メールアドレス</label>
                    <input type="email" id="email" class="form-control" placeholder="メールアドレスを入力してください">
                </div>

                <div class="mb-3">
                    <label for="phone">電話番号</label>
                    <input type="tel" id="phone" class="form-control" placeholder="電話番号を入力してください">
                </div>

                <div class="mb-3">
                    <label for="file">イベントの画像</label>
                    <input type="file" id="file" class="form-control">
                    <small class="form-text">JPEG形式のみ、5MBまで</small>
                </div>

                <div class="mb-3">
                    <label>イベントの言語:</label>
                    <div class="d-flex">
                        <div class="form-check custom-margin">
                            <input class="form-check-input" type="radio" name="language" id="english" value="english"
                                checked>
                            <label class="form-check-label" for="english">
                                英語
                            </label>
                        </div>
                        <div class="form-check">
                            <input class="form-check-input" type="radio" name="language" id="japanese" value="japanese">
                            <label class="form-check-label" for="japanese">
                                日本語
                            </label>
                        </div>
                    </div>
                </div>

                <div class="mb-3">
                    <label>イベントのカテゴリ:</label>
                    <div class="d-flex">
                        <div class="form-check custom-margin">
                            <input class="form-check-input" type="checkbox" name="categories" id="workshop"
                                value="workshop">
                            <label class="form-check-label" for="workshop">
                                ワークショップ
                            </label>
                        </div>
                        <div class="form-check custom-margin">
                            <input class="form-check-input" type="checkbox" name="categories" id="seminar"
                                value="seminar">
                            <label class="form-check-label" for="seminar">
                                セミナー
                            </label>
                        </div>
                        <div class="form-check">
                            <input class="form-check-input" type="checkbox" name="categories" id="conference"
                                value="conference">
                            <label class="form-check-label" for="conference">
                                カンファレンス
                            </label>
                        </div>
                    </div>
                </div>

                <div class="d-flex gap-3 justify-content-between">
                    <div class="mb-3 flex-fill mr-2">
                        <label for="startDateTime">開始日時:</label>
                        <input type="datetime-local" id="startDateTime" class="form-control">
                    </div>

                    <div class="mb-3 flex-fill ml-2">
                        <label for="endDateTime">終了日時:</label>
                        <input type="datetime-local" id="endDateTime" class="form-control">
                    </div>
                </div>

                <button type="submit" class="btn btn-primary">イベントを登録</button>
            </form>
        </section>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">テーブル</h2>
            <table class="table table-striped table-hover">
                <thead class="table-info">
                    <tr>
                        <th scope="col">プロフィール画像</th>
                        <th scope="col">名前</th>
                        <th scope="col">年齢</th>
                        <th scope="col">趣味</th>
                        <th scope="col">性別</th>
                        <th scope="col">興味</th>
                        <th scope="col">職業</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td class="align-middle"><img src="./image/admin.png" alt="田中太郎のプロフィール画像" width="100"></td>
                        <td class="align-middle">田中太郎</td>
                        <td class="align-middle">28</td>
                        <td class="align-middle">週末に山へハイキングに行ったり、読書をしたりすることが好きです。</td>
                        <td class="align-middle">男性</td>
                        <td class="align-middle">プログラミング, デザイン</td>
                        <td class="align-middle">エンジニア</td>
                    </tr>
                    <tr>
                        <td class="align-middle"><img src="./image/admin.png" alt="佐藤花子のプロフィール画像" width="100"></td>
                        <td class="align-middle">佐藤花子</td>
                        <td class="align-middle">24</td>
                        <td class="align-middle">映画を観ること、美味しいものを食べることが好きです。</td>
                        <td class="align-middle">女性</td>
                        <td class="align-middle">ファッション, 料理</td>
                        <td class="align-middle">デザイナー</td>
                    </tr>
                </tbody>
            </table>
        </section>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">カード一覧</h2>
            <div class="row">
                <div class="col-md-4">
                    <div class="card mb-4">
                        <img src="./image/admin.png" class="card-img-top" alt="管理者画像">
                        <div class="card-body">
                            <h5 class="card-title">カードのタイトル</h5>
                            <p class="card-text">このカードのタイトルに基づいて内容を簡単に示すテキスト。</p>
                            <a href="#" class="btn btn-primary">詳しく見る</a>
                        </div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card mb-4">
                        <img src="./image/admin.png" class="card-img-top" alt="管理者画像">
                        <div class="card-body">
                            <h5 class="card-title">カードのタイトル</h5>
                            <p class="card-text">このカードのタイトルに基づいて内容を簡単に示すテキスト。</p>
                            <a href="#" class="btn btn-primary">詳しく見る</a>
                        </div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card mb-4">
                        <img src="./image/admin.png" class="card-img-top" alt="管理者画像">
                        <div class="card-body">
                            <h5 class="card-title">カードのタイトル</h5>
                            <p class="card-text">このカードのタイトルに基づいて内容を簡単に示すテキスト。</p>
                            <a href="#" class="btn btn-primary">詳しく見る</a>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <section class="container blue-background p-3 mt-4">
            <h2 class="mb-3">グリッドシステム</h2>
            <div class="row mb-4">
                <div class="col-sm-3" style="background-color: green;">.col-sm-3</div>
                <div class="col-sm-3" style="background-color: red;">.col-sm-3</div>
                <div class="col-sm-3" style="background-color: gold;">.col-sm-3</div>
                <div class="col-sm-3" style="background-color: purple;">.col-sm-3</div>
            </div>
            <div class="row">
                <div class="col-sm-6" style="background-color: green;"> .col-sm-6</div>
                <div class="col-sm-2" style="background-color: red;">.col-sm-2</div>
                <div class="col-sm-2" style="background-color: gold;">.col-sm-2</div>
                <div class="col-sm-2" style="background-color: purple;">.col-sm-2</div>
            </div>
        </section>
    </div>

    <!-- External Scripts and Libraries -->
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.10.2/dist/umd/popper.min.js"
        integrity="sha384-pzjw8f+ua7Kw1TIq0v8FqFjcJ6pajs/rfdfs3SO+kD5tr5Szkbe5Ck4C2DgHfViX"
        crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.min.js"
        integrity="sha384-ka7Sk0Gln4gmtz2MlQ/5Jp3uhAclB+Z3pHaCtQqZtoPzn18bdcPg1oFjzylXjdG1"
        crossorigin="anonymous"></script>
    <script src="./scripts.js"></script>
</body>

</html>
```

```
/* リセットCSS */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.padded-container {
    padding: 20px;
    /* すべての方向に20pxのパディングを適用 */
}

.light-blue-background {
    background-color: #f0f7fc;
}

.blue-background {
    background-color: #dcedfc;
}

.custom-margin {
    margin-right: 15px;
}
```