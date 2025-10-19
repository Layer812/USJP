# 💻 USキーボードをWindows日本語キーボード環境で使う

## 📄 概要

会社支給PCなど、<b>システム権限がなくレジストリ変更や常駐ソフトの導入もできない</b>環境で、Happy Hacking Keyboardなどの外付けのUS配列キーボード**を**Windows日本語キーボード（JIS配列）**として使用するための設定手順です。

キーボードとPCの間に「**keyboard quantizer mini**」というアダプタを挟むことで、ハードウェアレベルでキーコードを変換し、OS側にはJIS配列として認識させます。
<img src="https://github.com/user-attachments/assets/8c11a08f-bf0b-4ff5-aa3b-28b2be5aa2ee" alt="Keyboard Quantizer Mini" style="max-width: 100%; height: auto;">

## ✨ 必要なもの

| 項目 | 説明 |
| :--- | :--- |
| **ハードウェア** | 遊舎工房の**keyboard quantizer mini** |
| **キーボード** | 使用したい**US配列キーボード** |
| **ソフトウェア** | Webベースの**Remap**ツールと**設定ファイル** |

## 🛠️ 設定手順

この手順により、USキーボードで押されたキーのコードを、物理的にJISキーボードの配列（コード）に変換します。

### ステップ 1: keyboard quantizer mini の初期設定（Bootloaderキーの設定）

まずは「keyboard quantizer mini」の設定変更モード（Bootloaderモード）に入るためのキーを割り当てます。

1.  Webブラウザで [Remap (https://remap-keys.app/)](https://remap-keys.app/) にアクセスします。
2.  keyboard quantizer miniをPCに接続し、Remap画面の指示に従って接続します。
3.  Remapのキーマップ編集画面で、Bootloaderに割り当てたいキーを選択します。
4.  左側のキーコードパレットから「**Device**」タブを選択し、「**Bootloader**」をドラッグ＆ドロップで選択したキーに割り当てます。
5.  右上の「**書き込み**」ボタンで設定がkeyboard quantizer miniに書き込まれます。
**Remap Bootloader設定とキーコード確認画面のイメージ:**
<img src="https://github.com/user-attachments/assets/3cee1c43-f321-44f3-8fb9-eb3fb9eddb59" alt="Remap Bootloader Key Assignment" style="max-width: 100%; height: auto;">

### ステップ 2: 設定用UF2ファイルの作成と投入

JIS配列への変換とキー入れ替えを行うための設定ファイルを作成し、アダプタに書き込みます。

1.  **設定ファイルの作成:**
    以下のWebサイトにアクセスし、設定ファイル（`config.uf2`）を作成します。
    [keyboard quantizer config generator](https://sekigon-gonnoc.github.io/keyboard-quantizer-config-generator/)
    
    以下の設定コードを入力します。
    
    ````yaml
    - application:
        keymaps:
          - layer:
              id: 0
              keys:
                KC_CAPS: KC_LCTL
    - default:
        keyboard_language: US
        os_language: JP
    ````
    このコードは以下の設定を行っています。
    * `keyboard_language: US` → `os_language: JP`: **USキーの入力コードを日本語キーボードのコードに変換**します。
    * `KC_CAPS: KC_LCTL`: USキーボードの**Caps Lockキーの位置を左Ctrlキーに変換**します。（よく行われるカスタマイズです）
    <img src="https://github.com/user-attachments/assets/6e4a8cc2-eb16-4af6-b321-8ce799b0dc8c" alt="Remap Bootloader Setting Screen" style="max-width: 100%; height: auto;">
    <a href = "https://github.com/Layer812/USJP/blob/main/CONFIG.UF2">こちらからもダウンロードできます。</a>
2.  **UF2ファイルのダウンロード:**
    コード入力後、画面の指示に従って`config.uf2`ファイルをダウンロードします。
3.  **Bootloaderモードでファイル投入:**
    keyboard quantizer miniをPCに接続した状態で、ステップ1で設定した**Bootloaderキーを押し**ます。
    * keyboard quantizer miniがUSBメモリのように認識され、ドライブが表示されます。
    * 現れたドライブに、ダウンロードした**`config.uf2`**ファイルをドラッグ＆ドロップで入れます。
    * ファイル投入後、アダプタが自動で再起動し、新しい設定が適用されます。

### ステップ 3: Fullモードのドライバ導入

Windowsで変換後のキーボードを正しく認識させるために、専用のドライバを導入します。

1.  以下のGitHubリリースぺージにアクセスします。
    [keyboard quantizer doc Releases - mini-3](https://github.com/sekigon-gonnoc/keyboard-quantizer-doc/releases/tag/mini-3)
2.  ページ内の`sekigon_keyboard_quantizer_mini_full.uf2`をダウンロードします。
3.  **Bootloaderモードでファイル投入:**
    keyboard quantizer miniをPCに接続した状態で、ステップ1で設定した**Bootloaderキーを押し**ます。
    * keyboard quantizer miniがUSBメモリのように認識され、ドライブが表示されます。
    * 現れたドライブに、ダウンロードした`sekigon_keyboard_quantizer_mini_full.uf2`ファイルをドラッグ＆ドロップで入れます。
    * ファイル投入後、アダプタが自動で再起動し、新しい設定が適用されます。

### 完了

設定を終えたkeyboard quantizer miniをPCとUSキーボードの間に挟むことで、OSからは日本語キーボードとして認識され、USキーボードの印字通りに押した記号などがOS上に表示されるようになります。

---

## ⚠️ 注意点

* **手順の順序:** 上記の**ステップ2（UF2ファイルの投入）**と**ステップ3（ドライバの導入）**の順序を間違えると、設定が適用されないなど、手間がかかる場合があります。必ず記載の順序で実施してください。
* **タイムラグ:** キーを押してから画面に文字が表示されるまでに、ごくわずかなタイムラグが発生する場合がありますが、一般的なBluetoothキーボードの遅延ほどではありません。
* **バージョン:** keyboard quantizer miniのファームウェアやドライバのバージョンアップにより、上記手順が変更される可能性があります。
* **無保証:** 本手順の実施により生じた不具合、損害などについて、筆者は一切の責任を負いません。自己責任にて実施してください。
