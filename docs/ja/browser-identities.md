<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Browser Identity

ブラウザーのログイン状態をIdentityごとに分離し、タスクとAutorunへ割り当て、取込・消去・削除する方法です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/browser-identities)

Browser Identityは、アプリ内ブラウザーのログイン状態とサイトデータを分けるローカルの永続領域です。仕事、顧客、検証条件ごとにIdentityを分けると、同じサイトへ異なるアカウントで安全に接続できます。

エージェントのアカウントプロファイルとは別の概念です。Browser IdentityはWebサイトの状態を分離し、エージェントプロファイルはClaude、Codexなどの実行認証を分離します。

## 分離されるデータ

Identityごとに次を分離します。

- Cookieとcache
- localStorageとsite permission
- proxy認証
- ブラウザーsessionとnavigation履歴
- passkeyとWebAuthnに関するIdentity側の状態

popupとOAuth windowは親タブのIdentityを引き継ぎます。別のIdentityへ切り替えてもデータはコピーされません。

## Default Identity

Identityを明示しないタスクとAutorunはDefault Identityを使います。Defaultは従来の`persist:in-app-browser`領域と既存のログイン状態を維持します。

Default Identityは名前と色を確認できますが、削除できません。ログインなしの公開状態を検証する場合は、DefaultのCookieを消すのではなく、専用の空のIdentityを作る方が安全です。

```bash
cockpit browser identity create --name logged-out-check
cockpit task browser-identity logged-out-check
cockpit browser open https://example.com --browser-identity logged-out-check --json
```

## Identityを作成・確認する

画面左下のアプリメニューから「Browser Identity」を開くと、Identityの作成、名前と色の変更、データ消去、削除を行えます。CLIでは次を使います。

```bash
cockpit browser identity list --in-use --json
cockpit browser identity get work --json
cockpit browser identity usages work --json
cockpit browser identity create --name work --color "#3B82F6" --json
cockpit browser identity update work --name client-a --color "#8B5CF6" --json
```

`list`は使用数を返し、すべて、使用中、未使用で絞り込めます。`get`と`usages`はIdentityのmetadata、使用数、参照しているタスク、Autorun、browser sessionを返します。

## タスクへ割り当てる

各タスクは一つのBrowser Identityを持ちます。新規タスクでは作成時に選択でき、既存タスクではブラウザーのサイドパネルまたはCLIから変更できます。

```bash
cockpit task browser-identity <taskId> work
cockpit task browser-identity <taskId>
```

タスク内でtask IDを省略すると、現在のタスクを変更します。切り替え後に開くsessionは新しいIdentityの領域を使います。既存sessionは元のIdentityに残り、元へ戻すと再び利用できます。

`cockpit browser open ... --browser-identity work`の指定は、現在の割り当てがworkであることを検証するだけで、割り当て自体を変更しません。別Identityのsessionまたはtab IDを指定すると拒否され、割り当て変更コマンドが案内されます。

## AutorunとFleetへ割り当てる

新規タスクを作るAutorunはBrowser Identityの割り当てを保存し、発火時に作成する各タスクへ引き継ぎます。Identityを指定しないAutorunはDefaultを使います。既存タスクへ送るAutorunは、そのタスクの割り当てを変更しません。

Fleetでは各task nodeへBrowser Identityを割り当てられます。異なるログイン状態が必要なnodeは、同じIdentityを共有させず、用途別のIdentityを明示します。

## macOSでChromeのsessionを取り込む

macOSでは、Chromeでログインを完了した後、選択中タブのregistrable domainに属するCookieと、その正確なoriginのlocalStorageを同じBrowser Identityへ取り込めます。

```bash
cockpit browser import-session --browser-identity work --profile "Profile 2" --json
```

`--browser-identity`はAGI Cockpit側の取込先、`--profile`はChrome側のprofileです。初回はmacOS Keychainが「Chrome Safe Storage」への許可を求める場合があります。

sessionStorage、IndexedDB、extension状態、device-bound認証、passkey自体は取り込みません。siteがログイン状態にならない場合は、そのIdentityのアプリ内ブラウザーで一度ログインしてください。`import-cookies`は互換性用でCookieだけを取り込むため、通常は`import-session`を使います。この取込はmacOSだけに対応します。

## データを消去・Identityを削除する

消去と削除は取り消せず、どちらも`--confirm`が必要です。

```bash
cockpit browser identity clear client-a --cookies --confirm --json
cockpit browser identity clear client-a --all --confirm --json
cockpit browser identity remove client-a --replace-with default --confirm --json
```

`clear`は対象Identityのlive sessionを閉じてから、all、cookies、cacheのいずれかを消去します。`remove`はすべての永続データを消去してIdentity自体を削除します。

実行中タスクまたはAutorunが参照するIdentityは、`--replace-with`で移行先を指定しない限り削除できません。移行後の削除に失敗した場合は割り当てを元へ戻します。完了済みタスクだけが参照しているIdentityを移行先なしで削除すると、それらの参照はDefaultへ戻ります。

削除前に`usages`で影響するタスク、Autorun、sessionを確認してください。

## 関連ページ

- [cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)
- [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)
- [Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)
- [セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)
