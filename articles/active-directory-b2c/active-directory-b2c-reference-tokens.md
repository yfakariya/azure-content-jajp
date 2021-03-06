<properties
	pageTitle="Azure Active Directory B2C プレビュー | Microsoft Azure"
	description="Azure Active Directory B2C プレビューで発行されるトークンの種類。"
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="dastrock"/>


# Azure AD B2C プレビュー: トークン リファレンス

Azure Active Directory (Azure AD) B2C は、各[認証フロー](active-directory-b2c-apps.md)を処理する際に、複数の種類のセキュリティ トークンを出力します。このドキュメントでは、各トークンの種類の形式、セキュリティ特性、内容について説明します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## トークンの種類

Azure AD B2C は [OAuth 2.0 承認プロトコル](active-directory-b2c-reference-protocols.md)をサポートしており、アクセス トークンと更新トークンの両方を利用します。また、[OpenID Connect](active-directory-b2c-reference-protocols.md) による認証とサインインもサポートしており、これによって 3 番目のトークンの種類 ID トークンが導入されます。これらの各トークンは、ベアラー トークンとして表されます。

ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。ベアラーは、トークンを提示できる任意の利用者を表します。Azure AD は、ベアラー トークンを受信するには、最初に利用者を認証する必要があります。ただし、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがありません。トランスポート層セキュリティ (HTTPS) などのセキュリティ保護されたチャネルで転送する必要があります。

ベアラー トークンがセキュリティ保護されたチャネル以外で転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。

ベアラー トークンのセキュリティに関する追加の考慮事項については、[RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750) をご覧ください。

Azure AD B2C によって発行されるトークンの多くは、JSON Web トークン (JWT) として実装されます。JWT は、2 つのパーティ間で情報を転送する、コンパクトで URL の安全な手段です。JWT には、"要求" と呼ばれる情報が含まれています。それらは、ベアラーに関する情報のアサーションと、トークンのサブジェクトです。JWT の要求は、伝送用にエンコードおよびシリアル化された JSON オブジェクトです。Azure AD B2C によって発行される JWT は署名されますが、暗号化されないため、JWT の内容を簡単に検査してデバッグできます。そのような作業に使用できるツールが、いくつかあります。たとえば、[calebb.net](https://calebb.net) などです。JWT の詳細については、「[JWT specifications (JWT の仕様)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)」を参照してください。

### ID トークン

ID トークンは、アプリが Azure AD B2C の `authorize` および `token` エンドポイントから受け取るセキュリティ トークンの形式です。ID トークンは [JWT](#types-of-tokens) として表され、アプリでユーザーを識別するために使用できる要求が含まれます。`authorize` エンドポイントから取得した ID トークンは、Web アプリケーションへのユーザーのサインインによく使用されます。`token` エンドポイントから取得した ID トークンは、同じアプリケーションまたはサービスの 2 つのコンポーネント間で通信するときに HTTP 要求に入れて送信されます。ID トークン内の要求は、自由に使用できます。一般的には、アカウント情報を表示するか、アプリでアクセス制御の決定を行うために使用されます。

ID トークンは署名されますが、この時点では暗号化されません。アプリや API が ID トークンを受け取ったら、トークンが認証済みであることを証明するために、[署名を検証](#token-validation)する必要があります。アプリまたは API は、トークンが有効であることを証明するために、いくつかの要求も検証する必要があります。シナリオの要件に応じて、アプリによって検証される要求は異なりますが、いずれのシナリオでも、アプリはいくつかの[共通の要求検証](#token-validation)を行う必要があります。

ID トークンの要求の詳細は、以下のサンプル ID トークンと併せて、このガイドの後の方の一覧に示されています。ID トークン内の要求は特定の順序では返されないことに注意してください。また、新しい要求が ID トークンに導入される可能性が常にあります。新しい要求が導入されたときに、アプリで問題が起きないようにする必要があります。一覧の要求は、この記事の執筆時点で、アプリで解釈できることが保証されているものです。実際には、[calebb.net](https://calebb.net) に貼り付けることによって、サンプル ID トークン内の要求を調べてみてください。さらに詳細な情報は、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)で参照できます。

#### サンプル ID トークン
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### ID トークン内の要求

Azure AD B2C では、トークンの内容を細かく制御できます。アプリの操作に必要な特定のユーザー データのセットを要求に入れて送信するように、[ポリシー](active-directory-b2c-reference-policies.md)を構成できます。これらの要求には、ユーザーの `displayName`、`emailAddress` などの標準プロパティを含めることができます。また、B2C ディレクトリで定義できる[カスタム ユーザー属性](active-directory-b2c-reference-custom-attr.md)を含めることもできます。受信した各 ID トークンには、特定のセットのセキュリティ関連要求が格納されます。アプリケーションはこれらの要求を使用して、ユーザーと要求を安全に認証できます。ここに示す要求は、Azure AD B2C によって発行される各 ID トークンに存在すると想定されるものです。どの要求が追加されるかは、ポリシーによって決まります。

| 名前 | 要求 | 値の例 | 説明 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 対象となる読者 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 受信者要求は、トークンの受信対象を識別します。ID トークンでは、受信者はアプリ登録ポータルでアプリに割り当てられた、アプリのアプリケーション ID です。アプリでは、この値を検証し、一致しない場合はトークンを拒否する必要があります。 |
| Issuer | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | この要求は、トークンを構築して返す Security Token Service (STS) を識別します。また、ユーザーが認証された Azure AD ディレクトリも識別します。アプリでは、発行者要求を検証し、トークンが v2.0 エンドポイントからのものであることを確認する必要があります。 |
| 発行時刻 | `iat` | `1438535543` | この要求は、トークンが発行された日時です。エポック時間で表されます。 |
| 期限切れ日時 | `exp` | `1438539443` | 期限切れ日時要求は、トークンが無効になる日時です。エポック時間で表されます。アプリでは、このクレームを使用してトークンの有効期間の有効性を確認する必要があります。 |
| 期間の開始時刻 | `nbf` | `1438535543` | この要求は、トークンが有効になる日時です。エポック時間で表されます。これは通常、トークンが発行されたときと同じ日時です。アプリでは、このクレームを使用してトークンの有効期間の有効性を確認する必要があります。 |
| バージョン | `ver` | `1.0` | これは ID トークンのバージョンです。Azure AD で定義されています。 |
| コード ハッシュ | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | コード ハッシュは、トークンが OAuth 2.0 認証コードと共に発行される場合にのみ、ID トークンに含まれます。これを使用して、認証コードの信頼性を検証できます。この検証の実行方法の詳細については、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)を参照してください。 |
| アクセス トークン ハッシュ | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | アクセス トークン ハッシュは、トークンが OAuth 2.0 アクセス トークンと共に発行される場合にのみ、ID トークンに含まれます。これを使用して、アクセス トークンの信頼性を検証できます。この検証の実行方法の詳細については、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)を参照してください。 |
| nonce | `nonce` | `12345` | nonce は、トークンのリプレイ攻撃を緩和するために使用される戦略です。アプリでは、`nonce` クエリ パラメーターを使用して、承認要求で nonce を指定できます。要求で指定した値は、ID トークンの `nonce` 要求に、変更されずに出力されます。これにより、アプリではこの値を要求で指定した値と比較して検証できます。この値は、アプリのセッションと特定の ID トークンを関連付けます。アプリでは、ID トークンの検証プロセス中に、この検証を実行する必要があります。 |
| [件名] | `sub` | `Not supported currently. Use oid claim.` | これは、トークンが情報をアサートするプリンシパルです (アプリのユーザーなど)。この値は変更不可で、再割り当ても再利用もできません。そのため、この値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。ただし、サブジェクト クレームは Azure AD B2C プレビューではまだ実装されていません。承認のサブジェクト要求を使用するのではなく、オブジェクト ID の `oid` 要求を含むようにポリシーを構成し、ユーザーの識別にはその値を使用する必要があります。 |
| 認証コンテキスト クラスの参照 | `acr` | `b2c_1_sign_in` | これは、ID トークンの取得に使用されたポリシーの名前です。 |
| 認証時刻 | `auth_time | `1438535543` | この要求は、ユーザーが資格情報を最後に入力した時刻です。エポック時間で表されます。 |



### アクセス トークン

現時点では、Azure AD B2C はアクセス トークンを発行しません。プレビュー段階では、2 者間の認証はサポートされていません。ただし、同じアプリケーションのコンポーネント間の通信に ID トークンを使用することはできます。[Azure AD B2C プレビューでサポートされている、アプリケーションと認証のシナリオ](active-directory-b2c-apps.md)について、さらに学習してください。

### 更新トークン

更新トークンは、OAuth 2.0 のフローで新しい ID トークンとアクセス トークンを取得するためにアプリで使用できるセキュリティ トークンです。更新トークンを使用すると、ユーザーが操作しなくても、ユーザーに代わってアプリがリソースへの長期的なアクセスを実現できます。

トークン応答で更新トークンを受け取るためには、アプリは `offline_acesss` スコープを要求する必要があります。`offline_access` スコープの詳細については、[Azure AD B2C のプロトコル リファレンス](active-directory-b2c-reference-protocols.md)をご覧ください。

更新トークンは、現在も将来的にも常に、アプリに対して完全に非透過的です。Azure AD によって発行され、Azure AD によってのみ検査および解釈できます。有効期間は長いですが、更新トークンが一定期間残っているという想定でアプリを記述しないでください。更新トークンは、いつでもさまざまな理由で無効になる可能性があります。アプリで更新トークンが有効かどうかを知る唯一の方法は、Azure AD に対してトークン要求を行って更新トークンを利用することです。

新しいトークンに対して更新トークンを利用すると (そして、アプリが `offline_access` スコープを許可されている場合は)、トークン応答で新しい更新トークンが返されます。新たに発行された更新トークンは、保存する必要があります。要求で使用した以前の更新トークンは、新しいものに置き換えます。これにより、可能な限り長く更新トークンが有効であることが保証されます。

## トークンの検証

現在、アプリにおいて実行する必要があるトークンの検証は、ID トークンの検証だけです。ID トークンを検証するには、アプリはトークンの署名と要求の両方を検証する必要があります。

<!-- TODO: Link -->
使用言語にもよりますが、JWT を検証するために、さまざまなオープン ソース ライブラリを利用できます。独自の検証ロジックを実装するより、これらのオープン ソース ライブラリを試してみることをお勧めします。このガイドの情報が、これらのライブラリを適切に使用する方法を学ぶために役立ちます。

### 署名の検証
JWT には 3 つのセグメントがあり、`.` 文字で区切られています。1 番目のセグメントは**ヘッダー**、2 番目は**本文**、3 番目は**署名**です。署名セグメントを使用して ID トークンの信頼性を検証し、アプリで信頼できることを確認できます。

ID トークンは、RSA 256 などの業界標準の非対称暗号アルゴリズムを使用して署名されます。ID トークンのヘッダーには、トークンの署名に使用されたキーと暗号方法に関する情報が含まれます。

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` 要求は、トークンの署名に使用されたアルゴリズムを示します。`kid` または `x5t` 要求は、トークンの署名に使用された特定の公開キーを示します。

いつでも、Azure AD は公開/秘密キー ペアの特定セットのいずれかを使用して、ID トークンに署名できます。Azure AD は定期的に使用可能なキー セットをローテーションするので、このキー変更を自動的に処理するようにアプリを作成する必要があります。Azure AD によって使用される公開キーの更新を確認する適切な頻度は、24 時間間隔です。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあります。これにより、アプリは実行時に Azure AD B2C に関する情報を取得できます。この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。B2C ディレクトリには、ポリシー別の JSON メタデータ ドキュメントがあります。たとえば、`fabrikamb2c.onmicrosoft.com` の `b2c_1_sign_in` ポリシーのメタデータ ドキュメントは次の場所にあります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

署名の検証に必要な署名キー データは、次の場所にある OpenID Connect メタデータのドキュメントを使用して入手できます。

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` はユーザーの認証に使用される B2C ディレクトリ、`b2c_1_sign_in` は ID トークンの取得に使用されるポリシーです。ID トークンの署名でどのポリシーが使用されたかと、メタデータをどこで取得できるかは、2 つの方法で判断できます。最初の方法では、ID トークンの `acr` 要求に含まれているポリシー名を確認します。本文を Base 64 でデコードし、結果の JSON 文字列を逆シリアル化することによって、JWT の本文から要求を解析できます。`acr` 要求は、ID トークンの発行に使用されたポリシーの名前です。もう 1 つの方法です。要求を発行するとき、`state` パラメーターの値に含まれるポリシーを符号化し、それから復号化して使用されたポリシーを判断します。どちらの方法も有効です。

メタデータ ドキュメントは、いくつかの便利な情報が含まれている JSON オブジェクトです。たとえば、OpenID Connect 認証を実行するために必要なエンドポイントの場所などです。また、トークンの署名に使用される公開キーのセットの場所を示す `jwks_uri` も含まれます。次に示すのがその場所ですが、メタデータ ドキュメントを使用して `jwks_uri` を解析することにより、動的にその場所をフェッチするのが最善の方法です。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

この URL に存在する JSON ドキュメントには、特定の時点で使用されているすべての公開キー情報が含まれます。アプリでは、`kid` または `x5t` 要求を JWT ヘッダーで使用して、特定のトークンの署名に使用される JSON ドキュメント内の公開キーを選択できます。その後、正しい公開キーと指定されたアルゴリズムを使用して、署名の検証を実行できます。

署名の検証を実行する方法については、このドキュメントでは説明していません。必要であれば、その作業を支援する多くのオープン ソース ライブラリを利用できます。

### 要求の検証
アプリまたは API は、ID トークンを受け取ったら、ID トークン内の要求に対していくつかのチェックを実行する必要があります。学習した内容は次のとおりです。

- **受信者**要求: これは、ID トークンが特定のアプリに渡されるように意図されていることを検証します。
- **期間の開始時刻**および**期限切れ日時**要求: これらは、ID トークンが期限切れでないことを検証します。
- **発行者**要求: これは、トークンが Azure AD によって特定のアプリに対して発行されたことを検証します。
- **nonce**: これは、トークンのリプレイ攻撃を緩和するための戦略です。

これらの要求に対して予期される値の詳細については、前の [ID トークンのセクション](#id-tokens)を参照してください。

## トークンの有効期間

以下のトークンの有効期間は、より詳細な知識が必要な場合のために提供されています。アプリの開発やデバッグの際に、役立つ場合があります。これらの有効期間が一定で変わらないものという想定でアプリを記述しないでください。変更される可能性があります。

| トークン | 有効期間 | 説明 |
| ----------------------- | ------------------------------- | ------------ |
| ID トークン | 1 時間 | 通常、ID トークンの有効期間は 1 時間です。Web アプリは、この有効期間を使用して、ユーザーとの独自のセッションを維持できます (推奨)。また、別のセッション有効期間を選択することもできます。アプリで新しい ID トークンを取得する必要がある場合は、Azure AD に対して新しいサインイン要求を行うだけで済みます。ユーザーに Azure AD との有効なブラウザー セッションがある場合は、資格情報を再度入力する必要はありません。 |
| 更新トークン | 最大 14 日 | 1 つの更新トークンは、最大 14 日間有効です。ただし、更新トークンは、さまざまな理由で、いつでも無効になる可能性があります。アプリは、要求が失敗するまで、またはアプリが更新トークンを新しいトークンに置き換えるまで、更新トークンの使用を試み続ける必要があります。更新トークンも、ユーザーが最後に資格情報を入力してから 90 日が経過すると無効になることがあります。 |
| 承認コード | 5 分 | 承認コードは、意図的に有効期間が短くなっています。受け取ったらすぐに、アクセス トークン、ID トークン、または更新トークンに対して使用する必要があります。 |

<!---HONumber=AcomDC_0518_2016-->