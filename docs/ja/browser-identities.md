<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Browser Identity

ブラウザーのログイン状態をIdentityごとに分離し、タスクとAutorunへ割り当て、取込・消去・削除する方法です。

> AGI Cockpit 4.79.0で2026-09-14に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/browser-identities)

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

画面左下のアプリメニューから「Browser」を開きます。左側の「Identity を追加」で作成し、Identityを選ぶとタブ一覧の上で名前と色を編集できます。変更は「保存」で反映します。データ消去と削除には確認が必要で、Defaultは削除できません。CLIでは次を使います。

```bash
cockpit browser identity list --in-use --json
cockpit browser identity get work --json
cockpit browser identity usages work --json
cockpit browser identity create --name work --color "#3B82F6" --json
cockpit browser identity update work --name client-a --color "#8B5CF6" --json
```

`list`は使用数を返し、すべて、使用中、未使用で絞り込めます。`get`と`usages`はIdentityのmetadata、使用数、参照しているタスク、Autorun、browser sessionを返します。

## タスクへ割り当てる

一つのタスクへ複数のBrowser Identityを割り当て、そのうち一つを指定省略時に使うプライマリIdentityにできます。各sessionとそのtabは必ず一つのIdentityに属します。タスク作成時に最初のIdentityを選び、ブラウザーのサイドパネルまたはCLIから割り当てを管理します。

```bash
cockpit task browser-identity <taskId> --add work
cockpit task browser-identity <taskId> --primary work
cockpit task browser-identity <taskId> --primary default
cockpit task browser-identity <taskId> --remove work
cockpit task browser-identity <taskId>
```

タスク内でtask IDを省略すると、現在のタスクを管理します。Identity名だけを渡すと、割り当て全体をその一件に置き換えます。プライマリIdentityの割り当てを外す前に、別の割り当て済みIdentityをプライマリにします。割り当てを外してもsession、tab、Cookie、保存データは残り、再度割り当てるとエージェントから操作できます。ほかのタスクには影響しません。

`cockpit browser open ... --browser-identity work`は割り当て済みのIdentityを選びます。割り当て自体や別Identityのtabは変更しません。未割り当てのIdentityへの操作は拒否されます。明示したsessionやtabと`--browser-identity`が異なる場合は`browser_identity_mismatch`、tabが明示した`--session`に属さない場合は`browser_session_mismatch`で失敗します。操作対象はコマンド開始時に確定するため、途中でプライマリIdentityやサイドパネルを切り替えても実行中の操作先は変わりません。

## AutorunとFleetへ割り当てる

新規タスクを作るAutorunはBrowser Identityの割り当てを保存し、発火時に作成する各タスクへ引き継ぎます。Identityを指定しないAutorunはDefaultを使います。既存タスクへ送るAutorunは、そのタスクの割り当てを変更しません。

Fleetでは各task nodeへBrowser Identityを割り当てられます。異なるログイン状態が必要なnodeは、同じIdentityを共有させず、用途別のIdentityを明示します。

## macOSでブラウザーのsessionを取り込む

macOSでは、Chrome、Brave、Edge、Arc、Vivaldi、Opera、Firefoxでログインを完了した後、選択中のアプリ内タブに対応する状態をそのBrowser Identityへ取り込めます。取込元を正確に指定する場合は、先に検出済みブラウザーとprofileを確認します。

```bash
cockpit browser import-sources --json
cockpit browser import-session --browser-identity work --browser brave --profile-id "Profile 2" --json
```

`--browser-identity`はAGI Cockpit側の取込先です。`--browser`は取込元ブラウザー、`--profile-id`は検出済みprofileを一意に指定します。代わりに`--profile`でprofile directoryまたはFirefoxのprofile名を照合できます。取込元を省略すると、検出済みブラウザーの優先profileから最も最近使われたものを選びます。

Chromium系では選択中タブのregistrable domainに属するCookieと、その正確なoriginのlocalStorageを取り込みます。ブラウザーごとに別の「Safe Storage」Keychain項目を使うため、そのブラウザーから初めて対象データを取り込むときはmacOSの許可が表示される場合があります。FirefoxはCookieだけを取り込み、Keychainへアクセスしません。localStorageが非対応であることは結果に表示されます。

sessionStorage、IndexedDB、extension状態、device-bound認証、passkey自体は取り込みません。siteがログイン状態にならない場合は、そのIdentityのアプリ内ブラウザーで一度ログインしてください。`import-cookies`は互換性用でCookieだけを取り込むため、通常は`import-session`を使います。この取込はmacOSだけに対応します。

取り込みは取込元ブラウザーにあるセッションのコピーであり、別アカウントへ新しくサインインする操作ではありません。取込元のアカウントを先に確認してください。取込後に取込元ブラウザーでログアウトすると、サイト側のセッション無効化によりCockpit側のコピーも使えなくなる場合があります。

## データを消去・Identityを削除する

消去と削除は取り消せず、どちらも`--confirm`が必要です。

```bash
cockpit browser identity clear client-a --cookies --confirm --json
cockpit browser identity clear client-a --all --confirm --json
cockpit browser identity remove client-a --replace-with default --confirm --json
```

`clear`は対象Identityのlive sessionを閉じてから、all、cookies、cacheのいずれかを消去します。`remove`はすべての永続データを消去してIdentity自体を削除します。

実行中タスクまたはAutorunが参照するIdentityは、`--replace-with`で移行先を指定しない限り削除できません。移行後の削除に失敗した場合は割り当てを元へ戻します。複数のIdentityを持つタスクでは削除対象だけを置き換え、対象がプライマリなら置換先をプライマリにします。完了済みタスクだけが参照するIdentityを移行先なしで削除すると、その割り当てを外し、プライマリを削除したタスクはDefaultへ戻ります。

削除前に`usages`で影響するタスク、Autorun、sessionを確認してください。

## 関連ページ

- [cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)
- [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)
- [Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)
- [セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)
