Hi there 👋

<!--
Twitter: [@kent7k](https://twitter.com/kent_0n)
**kent7k/kent7k** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->

<!--
<a href="https://github.com/anuraghazra/github-readme-stats">
  <img align="left" src="https://github-readme-stats-git-masterrstaa-rickstaa.vercel.app/api?username=kent7k&show_icons=true&include_all_commits=true&count_private=true&include_orgs=true&locale=en" />
</a>
<a href="https://github.com/anuraghazra/github-readme-stats">
  <img align="left" src="https://github-readme-stats.vercel.app/api/top-langs/?username=kent7k&show_icons=true&include_all_commits=true&count_private=true&include_orgs=true&locale=en" />
</a>
-->

---

## Claude Code を Windows（PowerShell）に入れる

管理者ではない普通の PowerShell で行う（「管理者として実行」だと別ユーザーの下に入ることがある）。PowerShell 5.1 のままでよい。

### 1. インストール

```powershell
irm https://claude.ai/install.ps1 | iex
```

`getaddrinfo ENOTFOUND downloads.claude.ai` で落ちたら、プロキシ経由の回線。2 へ。通ったら 3 へ。

### 2. プロキシ越しに入れる

使われているプロキシを調べる（PAC 自動設定でも実際の値が出る）:

```powershell
[System.Net.WebRequest]::GetSystemWebProxy().GetProxy('https://downloads.claude.ai')
```

- 別の URL（例 `http://proxy.example.com:8080`）が返る → それがプロキシ。下の値を書き換えて実行
- `https://downloads.claude.ai/` がそのまま返る → プロキシは無い。ネットワーク側で止められているので管理者に相談

```powershell
$env:HTTP_PROXY  = 'http://proxy.example.com:8080'
$env:HTTPS_PROXY = 'http://proxy.example.com:8080'
irm https://claude.ai/install.ps1 | iex
```

`claude` 本体も同じプロキシを使うので、ずっと効くように保存する:

```powershell
[Environment]::SetEnvironmentVariable('HTTP_PROXY',  'http://proxy.example.com:8080', 'User')
[Environment]::SetEnvironmentVariable('HTTPS_PROXY', 'http://proxy.example.com:8080', 'User')
```

### 3. PATH を通す

本体があるか:

```powershell
Test-Path "$env:USERPROFILE\.local\bin\claude.exe"
```

`True` なら PATH に足して、窓を全部閉じて開き直す:

```powershell
[Environment]::SetEnvironmentVariable("Path", [Environment]::GetEnvironmentVariable("Path","User") + ";$env:USERPROFILE\.local\bin", "User")
```

`False` なら別ユーザーに入っている。どこに入ったか探して、普通の窓で 1 からやり直す:

```powershell
Get-ChildItem C:\Users\*\.local\bin\claude.exe -ErrorAction SilentlyContinue
```

### 4. 確認とログイン

```powershell
claude --version
```

作業フォルダで起動し、ブラウザで Pro / Max のアカウントにログインする:

```powershell
claude
```

調子が悪いときの診断:

```powershell
claude doctor
```

### おまけ: PowerShell 7 を入れる（任意）

7 は 5.1 の更新ではなく別アプリとして並ぶ。開くときはスタートメニューの「PowerShell 7」か `pwsh`。

```powershell
winget install --id Microsoft.PowerShell --source winget
```

出典: <https://code.claude.com/docs/en/setup> ・ <https://code.claude.com/docs/en/troubleshoot-install>
