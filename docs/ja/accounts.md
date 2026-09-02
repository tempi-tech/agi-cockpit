<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# アカウントとAuto

複数のエージェントアカウントを分離して登録し、Auto、固定アカウント、実行中の切り替え、利用上限からの復旧を使う方法です。

> AGI Cockpit 4.68.0で2026-09-03に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/accounts)

AGI Cockpitのアカウントプロファイルは、同じエージェントプロバイダーへ複数のログインを分離して登録し、タスク、Autorun、Fleetごとに使い分ける機能です。Claude、Codex、Antigravity、Cursor、Qoder、Grok Buildに対応します。

## デフォルトと名前付きプロファイルを区別する

各プロバイダーには既存環境を使うデフォルトアカウントがあり、`work`や`personal`などの名前付きプロファイルを追加できます。プロファイル名はCockpit内の選択名であり、AIサービス側のプラン名やメールアドレスではありません。

アカウントプロファイルはBrowser Identityと別です。アカウントプロファイルはタスクを実行するClaudeやCodexなどの認証を分離し、Browser Identityはアプリ内ブラウザーで開いたWebサイトのCookieやlocalStorageを分離します。

## プロファイルを作成してログインする

SettingsのAgentsから対象エージェントのアカウントプロファイルを追加し、そのプロファイルへログインします。CLIではプロバイダーを`--agent-type`で指定します。

```bash
cockpit accounts
cockpit accounts create work --agent-type codex
cockpit accounts login work --agent-type codex
cockpit accounts list --agent-type codex
```

一覧にはプロバイダー、プロファイルID、名前、取得できる場合はメールアドレス、ログイン状態、利用状況、その取得時刻が表示されます。`auth_required`は再ログインが必要、`error`は利用状況を取得できない状態です。残量ゼロと同じ意味ではありませんが、Autoの実行先として安全に選べない場合があります。

Antigravityの名前付きプロファイルはブラウザーでGoogle OAuthを行い、OAuthトークン、会話、ログ、利用履歴をプロファイル専用の領域へ分離します。共有キーリングへのフォールバックは行いません。

## Autoを使う

新しいタスク、Autorun、Fleetでアカウントを選べる場合、既定は**Auto**です。Autoは名前付きプロファイル一件ではなく、実行時にログイン済みアカウントの利用状況を比較して選ぶ方法です。

Autoは、短い利用枠を含むすべての枠に残量があることを確認したうえで、最も遅くリセットされる利用枠をアカウント間の比較基準にします。その長期枠について、リセットまでの1分あたりに残っている割合が大きいアカウントを優先するため、期限が近く未使用分の多い週単位などの枠を先に活用できます。短い枠はアカウントを今すぐ利用できるかの判定に使い、長期枠の優先順位を上書きしません。

Autoで開始したタスクが利用上限や対応するプラン制限へ到達すると、利用可能な別アカウントを選び、保存済みセッションを移して処理を続けます。Claudeの利用クレジット枯渇とCodexのワークスペースクレジット枯渇も復旧対象です。別アカウントがない、利用状況を確認できない、切り替えや再開に失敗した場合は、理由を表示して人の対応を待ちます。

Autorunは実行のたびにAutoを解決し直します。Fleetは並列ノードで利用可能なアカウントを分散し、同じ一件へ集中しすぎないようにします。ただし、実際に同時実行できる量は各サービスの契約、利用上限、マシンの資源に従います。

## 固定アカウントを選ぶ

監査、顧客、請求、データ分離などの理由で実行先を固定する場合は、タスク作成時にプロファイル名を選びます。CLIでは`--account`を指定します。

```bash
cockpit task create --instruction "変更をレビューして" --directory /path/to/repo --agent-type codex --account work
cockpit autorun create --name "Daily review" --instruction "変更をレビューして" --directory /path/to/repo --agent-type codex --account work --type cron --expression "0 9 * * *"
```

固定アカウントは利用上限に達しても別アカウントへ自動切り替えしません。Autorunが保存した固定プロファイルを利用できなくなった場合、Cockpitは別のアカウントへ無言で置き換えず、Autorunを無効にして「要確認」を表示します。

## 実行中のタスクを切り替える

```bash
cockpit task account <task-id>
cockpit task account <task-id> work
cockpit task account <task-id> default
```

Claude、Codex、Grok Build、Antigravity、Cursor、Qoderでは、実行中タスクのアカウントを切り替えられます。Cockpitは現在のセッションを停止し、保存済み会話を対象プロファイルへ移して同じタスクを再開します。回答の生成中やタスク開始直後は切り替えられない場合があるため、タスクが入力を受け取れる状態になってから実行します。

利用上限で停止したタスクは、切り替え後に追加指示を送って続行します。切り替えは過去のプロバイダー側会話や請求を統合する操作ではありません。

## プロファイルを削除する

```bash
cockpit accounts remove work --agent-type codex --confirm
```

デフォルトアカウントは削除できません。名前付きプロファイルの削除には確認が必要で、タスクまたはAutorunが使用中の場合は拒否されます。先に対象タスクやAutorunをAuto、default、別のプロファイルへ変更してください。削除するとそのプロファイル固有の認証と保存データを再利用できなくなるため、対象のプロバイダーと名前を確認します。

エージェントごとの対応差は[エージェントとモデル](https://agi-labo.com/tools/cockpit/docs/agents-and-models)、タスク停止後の操作は[タスク詳細](https://agi-labo.com/tools/cockpit/docs/task-details)、正確なCLI契約は[`cockpit accounts` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/accounts)、認証データの境界は[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)を参照してください。
