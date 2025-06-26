---
highlighter: shiki
css: unocss
colorSchema: dark
transition: fade-out
mdc: true
layout: cover
lang: ja
glowSeed: 4
title: Laravel+PHPStanで始める
addons:
  - slidev-addon-graph
---

<h1 flex="~ col">
<div mt1 ml10 flex="~ col">
  <span flex="~ gap-2 items-center">
    Laravel+PHPStanで始める
  </span>
  <span flex="~ gap-2 items-center">
    実践的静的解析入門
  </span>
</div>
<div abs-br mb-8 mr-8 flex="~ col items-end">
  <img src="/img/phpcon-2025-logo-white.png" h-16 object-contain />
  <span text-xs opacity-60 mt-1>PHPカンファレンス2025 公式ロゴより</span>
</div>
</h1>

<!-- 
今日は「Laravel+PHPStanで始める実践的静的解析入門」というタイトルでお話しさせていただきます。よろしくお願いします。
 -->


---
layout: intro
glowSeed: 15
glowOpacity: 0.3
class: pl-25
---

# Naoki Haba

<div class="leading-8 opacity-90 text-lg space-y-2">
  <div class="flex items-center gap-3">
    <span class="i-carbon-building inline-block" />
    <span>株式会社 コドモン ソフトウェアエンジニア</span>
  </div>
  <div class="flex items-center gap-3">
    <span class="i-carbon-events inline-block" />
    <span>Vue Fes Japan 2025 コアスタッフ</span>
  </div>
  <div class="flex items-center gap-3">
    <span class="i-carbon-code inline-block" />
    <span>OSS活動: Vue.js エコシステムへのコントリビュート</span>
  </div>
</div>

<div class="mt-8 flex gap-6 text-base">
  <div class="flex items-center gap-2">
    <span class="i-carbon-logo-x" />
    <span>@naohaba70</span>
  </div>
  <div class="flex items-center gap-2">
    <span class="i-carbon-logo-github" />
    <span>NaokiHaba</span>
  </div>
</div>

<img src="https://avatars.githubusercontent.com/u/59875779" rounded-full w-80 abs-tr mt-32 mr-22 />

<!-- 
まず最初に、軽く自己紹介をさせていただきます。羽馬 直樹と申します。株式会社コドモンでソフトウェアエンジニアをしています。 Vue Fes Japan 2025 のコアスタッフもやっています。
 -->

---
layout: default
---

# PHPStan

<div class="mt-4">
  <div v-click class="flex justify-center mb-6">
    <img src="/img/phpstan-log.png" class="h-60" />
  </div>
  
  <div v-click class="text-center space-y-4">
    <div class="text-4xl font-bold text-gradient bg-gradient-to-r from-blue-400 to-purple-600">
      PHPの静的解析ツール
    </div>
  </div>
  
  <div v-click class="text-center mt-8 text-base opacity-60">
    <a href="https://github.com/phpstan/phpstan" target="_blank" class="hover:opacity-100 transition-opacity">
      github.com/phpstan/phpstan
    </a>
  </div>
</div>

<!-- 
まずはPHPStanについてあまり知らないという方もいらっしゃると思いますので、PHPStanについて簡単に紹介させてください。
PHPStanは、PHPの静的解析ツールです。コードを実行する前に潜在的なバグや型エラーを検出し、コードの品質向上に役立ちます。
 -->

---
layout: default
---

# Larastan

<div class="mt-2">
  <div v-click class="flex justify-center mb-4">
    <img src="/img/larastan-logo.png" class="h-48" />
  </div>
  
  <div v-click class="text-center space-y-3">
    <div class="text-4xl font-bold text-gradient bg-gradient-to-r from-red-500 to-orange-500">
      Laravel専用のPHPStan拡張
    </div>
  </div>
  
  <div v-click class="text-center mt-8 text-base opacity-60">
    <a href="https://github.com/larastan/larastan" target="_blank" class="hover:opacity-100 transition-opacity">
      github.com/larastan/larastan
    </a>
  </div>
</div>

<!-- 
Laravelプロジェクトでは、専用拡張の「Larastan」を使うことで、Eloquentモデルやファサードなど、Laravel特有の機能に対応した精密な解析が可能になります。
 -->

---
layout: default
---

# 導入方法

<div class="space-y-8">

<div v-click>

## <span class="text-gradient bg-gradient-to-r from-blue-400 to-purple-600">① Larastanのインストール</span>

```bash
$ composer require --dev "larastan/larastan:^3.0"
```

</div>

<div v-click>

## <span class="text-gradient bg-gradient-to-r from-blue-400 to-purple-600">② 設定ファイルの作成</span>

<div class="flex items-center gap-2 mb-2">
  <span class="text-sm opacity-70">phpstan.neon</span>
  <span class="text-xs px-2 py-1 bg-blue-500/20 rounded">必須</span>
</div>

```yaml
includes:
    - vendor/larastan/larastan/extension.neon
    - vendor/nesbot/carbon/extension.neon

parameters:
    paths:
        - app/
    
    # Level 10 is the highest level
    level: 5
```

</div>

</div>

<!-- 
導入方法についても、非常にシンプルです。Composerを使って必要なパッケージをインストールし、設定ファイルを作成するだけで始められます。
 -->

---
layout: default
---

<div class="space-y-6">

<div class="text-center mb-8">
  <h2 class="text-2xl font-bold text-gradient bg-gradient-to-r from-blue-400 to-purple-600">
    存在しないプロパティの検出
  </h2>
</div>

<div class="relative">

```php {||17}
class User extends Authenticatable
{
    use HasFactory, Notifiable;
    
    protected $fillable = [
        'name',
        'email', 
        'password',
    ];
}

class UserController extends Controller
{
    public function updateUserStatus($userId)
    {
        $user = User::find($userId);
        $user->status = 'active';
        $user->save();
    }
}
```

<div v-click="3" class="absolute top-32 right-8 w-80 p-3 bg-red-500/10 rounded-xl border border-red-500/20 shadow-lg">
  <div class="font-bold text-red-400 mb-1 text-base">🚨 PHPStanエラー</div>
  <code class="text-xs block mb-1 p-2 bg-red-900/20 rounded font-mono">App\Models\User->$status property not found</code>
  <div class="text-xs opacity-80">$statusプロパティが定義されていません</div>
</div>

</div>

</div>

<!-- 
それでは、PHPStanが実際にどのような問題を検出してくれるのか、具体的な例を見ていきましょう。まず最初は、存在しないプロパティの検出についてです。

このコードを見てください。Userモデルには、name、email、passwordのプロパティは定義されていますが、statusプロパティは定義されていません。しかし、UserControllerでは$user->statusに値を代入しようとしています。

通常のPHPの実行時には、これは動的にプロパティが作成されるだけでエラーになりませんが、これは意図しない動作かもしれません。PHPStanを使うことで、このような存在しないプロパティへのアクセスを事前に検出できます。
 -->

---
layout: default
---

<div class="space-y-8">

<div class="text-center mb-8">
  <h2 class="text-2xl font-bold text-gradient bg-gradient-to-r from-blue-400 to-purple-600">
    返却値の型チェック
  </h2>
</div>

<div class="relative">

```php {||9|6}
class UserService
{
    /**
     * ユーザーの年齢を計算する
     */
    public function calculateAge(string $birthDate): int
    {
        $date = new DateTime($birthDate);
        return $birthDate;
    }
    
    /**
     * ユーザープロフィールを更新する
     */
    public function updateProfile(int $userId, array $data): bool
    {
        $user = User::find($userId);
        $user->update($data);
        return true;
    }
}
```

<div v-click="2" class="absolute top-20 right-8 w-80 p-4 bg-red-100 dark:bg-red-900/20 rounded-xl border border-red-300 dark:border-red-500/30 shadow-lg">
  <div class="relative">
    <div class="absolute -left-4 top-6 w-0 h-0 border-t-8 border-b-8 border-r-16 border-transparent border-r-red-100 dark:border-r-red-900/20"></div>
    <div class="text-sm font-medium text-red-700 dark:text-red-300 mb-2">❌ 型の不整合エラー</div>
    <div class="text-sm text-red-600 dark:text-red-400">
      返り値の型が一致しません<br/>
      <code class="bg-red-200 dark:bg-red-800 px-1 rounded text-xs">string</code> → <code class="bg-red-200 dark:bg-red-800 px-1 rounded text-xs">int</code>
    </div>
  </div>
</div>

</div>

</div>

<!-- 
次に、返却値の型チェックについて見てみましょう。これは関数やメソッドの戻り値の型が、宣言された型と一致しているかをチェックする機能です。

この例では、calculateAgeメソッドの戻り値型がintと宣言されているにも関わらず、実際には$birthDate（つまりstring）を返しています。これは明らかな型の不整合ですね。

PHPStanはこのような型の不整合を検出し、実行前に問題を発見してくれます。これにより、型に関連するバグを未然に防ぐことができます。
 -->

---
layout: default
---

<div class="space-y-8">

<div class="text-center mb-8">
  <h2 class="text-2xl font-bold text-gradient bg-gradient-to-r from-blue-400 to-purple-600">
    Nullポインタ例外の防止
  </h2>
</div>

<div class="relative">

```php {||8}
class UserManagementService
{
    /**
     * ユーザーのステータスを更新する
     */
    public function updateUserStatus($userId, string $status): bool
    {
        $user = User::find($userId);
        $user->update(['status' => $status]);
        $this->sendStatusUpdateEmail($user);
        
        return true;
    }
    
    /**
     * ステータス更新メールを送信
     */
    private function sendStatusUpdateEmail(User $user): void
    {
        Mail::to($user->email)->send(new StatusUpdatedMail($user));
    }
}
```

<div v-click="1" class="absolute top-20 right-8 w-80 p-4 bg-orange-100 dark:bg-orange-900/20 rounded-xl border border-orange-300 dark:border-orange-500/30 shadow-lg">
  <div class="relative">
    <div class="absolute -left-4 top-6 w-0 h-0 border-t-8 border-b-8 border-r-16 border-transparent border-r-orange-100 dark:border-r-orange-900/20"></div>
    <div class="text-sm font-medium text-orange-700 dark:text-orange-300 mb-2">⚠️ Null安全性の問題</div>
    <div class="text-sm text-orange-600 dark:text-orange-400">
      $userがnullの可能性があります<br/>
      nullチェックが必要です
    </div>
  </div>
</div>

</div>

</div>

<!-- 
続いて、Nullポインタ例外の防止について説明します。これは特に重要な機能で、多くのランタイムエラーを防ぐことができます。

このコードでは、User::findメソッドを使ってユーザーを検索していますが、find メソッドは指定されたIDのユーザーが存在しない場合、nullを返す可能性があります。しかし、その後の処理では$userがnullでないことを前提としてメソッドを呼び出しています。

PHPStanはこのような「nullの可能性がある変数に対してメソッドを呼び出している」ケースを検出し、適切なnullチェックを行うよう警告してくれます。これにより、実行時のNullPointerExceptionを防ぐことができます。
 -->

---
layout: default
---

# Collection処理の課題と解決策

<div class="grid grid-cols-2 gap-4">

<div v-click>

## ⚠️ 課題：型推論の限界

```php {6-8}
public function analyzeUsers($users)
{
    return $users
        ->filter(fn($user) => $user->is_active)
        ->groupBy(fn($user) => $user->department)
        ->map(fn($group) => [
            'count' => $group->count(),
            'average_age' => $group->avg('age')
        ]);
}
```

<div class="mt-2 text-sm text-orange-500">
Collectionチェーンで型情報が失われやすい
</div>

</div>

<div v-click>

## ✅ 解決策：型アノテーション

```php {1-4}
/**
 * @param Collection<int, User> $users
 * @return Collection<string, array{count: int, average_age: float}>
 */
public function analyzeUsers(Collection $users)
{
    return $users
        ->filter(fn(User $user) => $user->is_active)
        ->groupBy(fn(User $user) => $user->department)
        ->map(fn(Collection $group) => [
            'count' => $group->count(),
            'average_age' => $group->avg('age')
        ]);
}
```

<div class="mt-2 text-sm text-green-500">
PHPDocで詳細な型情報を追加
</div>

</div>

</div>

<!-- 
Laravelでよく使うCollectionのメソッドチェーンは、PHPStanにとって型推論が難しい部分です。

左側のコードでは、filter、groupBy、mapと連鎖させていますが、各段階でどんな型が流れているか静的に解析するのが困難です。

解決策は右側のように、PHPDocコメントで詳細な型アノテーションを追加することです。Collection<int, User>のように、中身の型まで明示することで、Larastanが正確に型を追跡できるようになります。
 -->

---
layout: default
---



# 既存プロジェクトへの段階的導入：Baseline機能

<div class="space-y-8">

<div v-click class="p-6 bg-gradient-to-br from-orange-500/10 to-red-500/10 rounded-xl border border-orange-500/20">
  <h2 class="text-2xl font-bold mb-4 text-orange-400">⚠️ 既存プロジェクトでの課題</h2>
  <p class="text-lg opacity-90">
    PHPStanは優れた機能ですが、途中から導入すると
    <span class="font-bold text-red-400">大量のエラーに悩まされる</span>ことがあります
  </p>
</div>

<div v-click class="p-6 bg-gradient-to-br from-blue-500/10 to-purple-500/10 rounded-xl border border-blue-500/20">
  <h2 class="text-2xl font-bold mb-4">🎯 そのためのBaseline機能</h2>
  <p class="text-lg opacity-90">
    既存プロジェクトの現在のエラーを「ベースライン」として記録し、
    <span class="font-bold text-blue-400">新しいエラーのみを検出</span>する機能
  </p>
</div>

</div>

<!-- 
さて、ここまでPHPStanの素晴らしい機能について説明してきましたが、実際に既存のプロジェクトに導入しようとすると、ある問題に直面することがあります。

それは、既存のコードベースでPHPStanを実行すると、大量のエラーが検出されてしまうことです。これは決して悪いことではありませんが、すべてのエラーを一度に修正するのは現実的ではありません。

そこで登場するのがBaseline機能です。この機能により、既存のエラーをベースラインとして記録し、新しく追加されるエラーのみを検出することができます。つまり、既存のコードに手を加えることなく、新しいコードの品質だけを厳密にチェックできるのです。
 -->

---
layout: default
---

# Baseline機能の使用方法とメリット

<div class="space-y-8">

<div v-click>

## <span class="text-gradient bg-gradient-to-r from-blue-400 to-purple-600">使用方法</span>

<div class="grid grid-cols-2 gap-6">

<div>

### 1. Baselineファイルの生成

```bash
./vendor/bin/phpstan analyse \
  --generate-baseline
```

</div>

<div>

### 2. 設定ファイルに追加

```yaml
parameters:
    baseline: phpstan-baseline.neon
```

</div>

</div>

</div>

<div v-click>

## <span class="text-gradient bg-gradient-to-r from-green-400 to-emerald-600">段階的な品質改善が可能</span>

<div class="grid grid-cols-3 gap-6 mt-6">
  <div class="p-6 bg-green-500/10 rounded-lg border border-green-500/20">
    <div class="font-bold mb-3 text-lg">📈 段階的改善</div>
    <div class="text-sm opacity-80">既存エラーを一度に修正する必要なし</div>
  </div>
  <div class="p-6 bg-blue-500/10 rounded-lg border border-blue-500/20">
    <div class="font-bold mb-3 text-lg">✨ 新規コードの品質</div>
    <div class="text-sm opacity-80">新しく書くコードのみ厳密チェック</div>
  </div>
  <div class="p-6 bg-purple-500/10 rounded-lg border border-purple-500/20">
    <div class="font-bold mb-3 text-lg">👥 チーム導入</div>
    <div class="text-sm opacity-80">既存コードを壊すリスクなし</div>
  </div>
</div>

</div>

<div v-click="3" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
  <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-2xl max-w-lg mx-4 border border-orange-200 dark:border-orange-700">
    <h3 class="text-xl font-bold text-orange-800 dark:text-orange-200 mb-4 text-center">📊 実際の運用例</h3>
    <div class="space-y-4 text-base text-gray-700 dark:text-gray-300">
      <div class="p-4 bg-gray-100 dark:bg-gray-700 rounded-lg">
        <div class="font-mono text-sm">
          <div class="text-blue-600 dark:text-blue-400"># 初回：ベースライン生成</div>
          <div>./vendor/bin/phpstan analyse --generate-baseline</div>
          <div class="text-green-600 dark:text-green-400 mt-2"># → 既存の1000個のエラーを記録</div>
        </div>
      </div>
      <div class="p-4 bg-gray-100 dark:bg-gray-700 rounded-lg">
        <div class="font-mono text-sm">
          <div class="text-blue-600 dark:text-blue-400"># 新機能開発後：新しいエラーのみ表示</div>
          <div>./vendor/bin/phpstan analyse</div>
          <div class="text-green-600 dark:text-green-400 mt-2"># → 新規追加の2個のエラーのみ検出</div>
        </div>
      </div>
    </div>
  </div>
</div>

</div>

<!-- 
Baseline機能の使用方法は非常にシンプルです。

まず、--generate-baselineオプションを付けてPHPStanを実行します。これにより、現在のプロジェクトで検出されるすべてのエラーがphpstan-baseline.neonファイルに記録されます。

次に、設定ファイルでこのベースラインファイルを指定します。これで設定は完了です。

この機能の最大のメリットは、段階的な品質改善が可能になることです。既存のエラーを一度に修正する必要がなく、新しく書くコードのみを厳密にチェックできます。また、チーム開発においても、既存のコードを壊すリスクなしに導入できるため、非常に実用的です。

実際の運用例を見てみましょう。初回実行時に1000個のエラーがベースラインとして記録されたとします。その後、新機能を開発してPHPStanを実行すると、新規追加の2個のエラーのみが表示されます。これにより、新しいコードの品質を確実に保つことができるのです。
 -->

---
layout: default
---

# まとめ

<div class="space-y-8">

## Laravel + PHPStanの効果

<div class="grid grid-cols-2 gap-8 mt-8">

<div class="p-6 bg-gradient-to-br from-red-500/10 to-pink-500/10 rounded-xl border border-red-500/20">
  <div class="text-2xl mb-3">🐛</div>
  <div class="font-bold text-lg mb-2">早期バグ発見</div>
  <div class="text-sm opacity-80">実行前に問題を検出</div>
</div>

<div class="p-6 bg-gradient-to-br from-blue-500/10 to-indigo-500/10 rounded-xl border border-blue-500/20">
  <div class="text-2xl mb-3">👥</div>
  <div class="font-bold text-lg mb-2">チーム開発の品質向上</div>
  <div class="text-sm opacity-80">コードレビューの負担軽減</div>
</div>

<div class="p-6 bg-gradient-to-br from-purple-500/10 to-violet-500/10 rounded-xl border border-purple-500/20">
  <div class="text-2xl mb-3">🔒</div>
  <div class="font-bold text-lg mb-2">型安全性の向上</div>
  <div class="text-sm opacity-80">PHPに更なる型の恩恵を受けられる</div>
</div>

<div class="p-6 bg-gradient-to-br from-green-500/10 to-emerald-500/10 rounded-xl border border-green-500/20">
  <div class="text-2xl mb-3">✅</div>
  <div class="font-bold text-lg mb-2">リファクタリングの安心感</div>
  <div class="text-sm opacity-80">変更の影響を即座に把握</div>
</div>

</div>

</div>

<!-- 
最後に、今日お話しした内容をまとめさせていただきます。

Laravel + PHPStanの組み合わせは、現代のPHP開発において非常に強力なツールです。まず、早期バグ発見により、実行前に問題を検出できます。これにより、本番環境でのトラブルを大幅に減らすことができます。

また、チーム開発においても大きなメリットがあります。コードレビューの負担が軽減され、一定の品質基準を自動的に保つことができます。

型安全性の向上により、PHPでもより堅牢なアプリケーションを構築できるようになります。そして、リファクタリング時の安心感も得られます。変更の影響を即座に把握できるため、大規模な改修作業も安心して行えます。

Baseline機能を活用することで、既存プロジェクトでも段階的に導入できるため、ぜひ皆さんのプロジェクトでも試してみてください。
 -->


---
layout: end
---

# ご清聴ありがとうございました！
