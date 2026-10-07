# zmk-config-LiNEA40（DYA Studio 対応版）

LiNEA40 の ZMK ファームウェア設定です。[DYA Studio](https://studio.dya.cormoran.works/) に対応しており、ファームウェアを書き換えずにブラウザからキーマップ・マクロ・コンボ・トラックボール・接続先などを変更できます。

- ZMK: [cormoran/zmk `main+dya`](https://github.com/cormoran/zmk/tree/main%2Bdya)（Zephyr 4.1）
- ボード: `xiao_ble//zmk`（Seeed XIAO nRF52840）
- 右手側が central（PC・DYA Studio と通信する側）、左手側が peripheral

## DYA Studio でできること

| タブ | 内容 | 使用モジュール |
| --- | --- | --- |
| Keymap | キー割り当て・レイヤーの編集、押下キーの表示、ロータリーエンコーダーの割り当て | ZMK Studio / fast-keymap / input-stream / module-physical-layout / runtime-sensor-rotate |
| Macro / Combo | マクロ・コンボの作成と編集 | runtime-macro / runtime-combo |
| Trackball | カーソル速度・回転・軸反転・スクロール・オートマウス、PMW3610 の CPI や省電力設定 | runtime-input-processor / pmw3610-with-custom-studio-rpc |
| Connection | BLE プロファイル管理、接続先・OS ごとのデフォルトレイヤー | ble-management / default-layer / os-detection |
| Settings | 各種設定 | settings-rpc / custom-settings |
| Troubleshooting | ファームウェア情報、再起動原因の確認、キースイッチ診断 | device-info / watchdog / kscan-diagnostics |

## 書き込み手順

ZMK のバージョンが大きく変わるため、初回は設定のリセットから行ってください。

1. GitHub の Actions から最新ビルドの `firmware` をダウンロードして展開する
2. 左右それぞれに `settings_reset.uf2` を書き込む（リセットボタンを 2 回押してドライブに入れる）
3. 左手側に `LiNEA40_left.uf2`、右手側に `LiNEA40_right.uf2` を書き込む
4. PC / スマホ側に残っている古い「LiNEA40」のペアリングを削除し、ペアリングし直す

## DYA Studio の使い方

1. Chrome / Edge で <https://studio.dya.cormoran.works/> を開く
2. **右手側**を USB でつないで接続する
3. キーボードで Studio Unlock を実行する
   - 親指の SPACE + ENTER（キー位置 35・36）のコンボを押したままレイヤー 6（WIRELESS）に入り、BACKSPACE の位置のキー（キー位置 37、`&studio_unlock`）を押す
4. 編集して保存する

一定時間操作しない、または切断すると再びロックされます。

## トラックボールの初期設定

以前のファームウェアと同じ挙動になるよう初期値を入れています。いずれも DYA Studio の Trackball タブから変更できます。

| processor | 役割 | 初期値 |
| --- | --- | --- |
| `mouse` | カーソル移動。動かすとレイヤー 1（MOUSE）を自動で有効化 | 等倍、600ms で解除 |
| `snipe` | レイヤー 2（MARK）の間だけカーソルを減速 | 1/2 倍 |
| `scroll` | レイヤー 5（SCROLL）の間だけカーソル移動をスクロールに変換 | 1/3 倍、縦方向優先 |

センサーの初期 CPI は 800 です。向きが合わない場合は Trackball タブで軸の反転・回転を調整してください。

## 補足

- コンボは `config/LiNEA40.keymap` の `runtime_combo_defaults` に初期値として定義しています。Combo タブに表示され、そのまま編集できます（最大 16 個）。Studio で変更したコンボは Studio 側の値が優先され、「Reset to Default」でこの初期値に戻ります。
- ロータリーエンコーダーはレイヤーごとに `rsr_*` で初期値を定義しています。Studio で割り当てたレイヤーは Studio 側の値が優先されます。
- Web の Keymap Editor はコンボとエンコーダーの定義を扱えなくなります。コンボとエンコーダーは DYA Studio から変更してください。
- バッテリー履歴・開発者ツール（devtool）・スリープ時間の設定は入れていません。
- DYA Studio のプレビューに出るトラックボールとロータリーエンコーダーの位置は目安です（`LiNEA40.dtsi` の `trackball_layout` / `left_encoder_layout`）。
- `config/west.yml` の各モジュールは `main` ブランチを追従します。上流の変更でビルドが通らなくなった場合は、動いていたコミットに `revision` を固定してください。
- DYA Studio 対応前の状態はタグ `pre-dya-studio` に残しています。

## 参考

- [DYA Studio 開発者ガイド](https://studio.dya.cormoran.works/developer-guide)
- [cormoran/zmk-config-dya-studio-sample](https://github.com/cormoran/zmk-config-dya-studio-sample)
- [cormoran/zmk-keyboard-dya2](https://github.com/cormoran/zmk-keyboard-dya2)
