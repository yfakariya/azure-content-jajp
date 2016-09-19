<properties
   pageTitle="Azure のリソースについて推奨される命名規約 | ガイダンス | Microsoft Azure"
   description="Azure のリソースについて推奨される命名盟約です。仮想マシン、ストレージ アカウント、ネットワーク、仮想ネットワーク、サブネット、およびその他の Azure エンティティの命名方法です"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# Azure のリソースについて推奨される命名規約

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

以下の理由から、Microsoft Azure における何らかのリソースについて、名前の選択は重要です：

- 後から名前を変えるのは大変です。
- 名前は、リソースの種類ごとに異なる要件に従っていなければなりません。

一貫した命名規約によって、リソースの配置が簡単になります。そのような規約は、ソリューションにおけるリソースの役割を示すこともできます。

この記事は、Azure のリソースの命名に関するルールと制約事項についての概要と、命名規約の推奨事項のベースライン セットです。
ニーズごとに異なる自前の規約のとっかかりとして、これらの推奨事項を使用できます。

命名規約のコツは、アプリケーションと組織の全体を通して定めることと、それに従うことです。

## サブスクリプションの命名

Azure サブスクリプションに名前を付けるときには、冗長な名前を付けることで、それぞれのサブスクリプションの文脈と目的をはっきりと理解できるようになります。
多数のサブスクリプションを使用する環境で作業している場合、共有された命名規約に従うことで、わかりやすさを改善できます。

サブスクリプションの命名の推奨されたパターンは次のとおりです：

`<会社名> <部署名（省略可能）> <製品ライン（省略可能）> <環境>`

- 会社名は、通常、各サブスクリプションで同じになるでしょう。ただし、会社によっては組織構造の中に子会社が含まれる場合があります。
それらの会社は集約された IT グループによって管理されることがあります。
そのような場合、親会社の名前（*Contoso*）と子会社の名前（*North Wind*）の両方を含めることで区別できるでしょう。

- 部署名は、別の仕事をしているグループの、組織内での名前です。名前空間にこの項目を含めなくてもかまいません。

- 製品ラインは、その部署における、製品または実行される機能に固有の名前です。組織で利用するサービスやアプリケーションでは、通常この項目は含めなくてもかまいません。
ただし、簡単に区別や識別ができること（たとえば、請求書の項目がはっきり分かれていること）が必要になる公開サービスについては、この項目の使用が強く推奨されます。

- 環境はアプリケーションやサービスのデプロイメント ライフサイクルを説明する名前です。たとえば、Dev、QA、Prod などです。

| 会社名 | 部署名 | 製品ラインまたはサービス | 環境 | 完全な名前  |
|---------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Production | Contoso SocialGaming AwesomeService Production |
| Contoso | SocialGaming | AwesomeService | Dev | Contoso SocialGaming AwesomeService Dev |
| Contoso | IT | InternalApps | Production | Contoso IT InternalApps Production |
| Contoso | IT | InternalApps | Dev | Contoso IT InternalApps Dev |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## あいまいさを避けるために接頭辞と接尾辞を使用する

Azure でリソースに名前を付けるときには、そのリソースの種類と文脈を特定するために、共通の接頭辞や接尾辞の使用が推奨されます。
種類、メタデータ、文脈に関する情報はすべてプログラムを使用して取得できますが、共通の接頭辞や接尾辞を使用することで、見た目の区別が単純になります。
命名規約に接頭辞と接尾辞を組み入れる場合、それらが名前の前に付けるもの（接頭辞）なのか後に付けるもの（接尾辞）なのかをはっきりと示すことが重要です。

たとえば、計算エンジン（CalculationEngine）をホストするサービスについて、名前の候補は 2 つあります：

- SvcCalculationEngine （接頭辞）
- CalculationEngineSvc （接尾辞）

接頭辞と接尾辞は、特定のリソースを説明する、様々な側面を指し示すことができます。典型的な使用例を以下の表に示します：

| 側面 | 例 | 説明 |
| ------ | ------- | ----- |
| 環境 | dev, prod, QA | リソースの環境を識別します。 |
| 場所 | uw (US West), ue (US East) | リソースがデプロイされるリージョンを識別します。 |
| インスタンス | 01, 02 | 複数の名前付きインスタンスのあるリソース用です（Web サーバーなど）。 |
| 製品またはサービス | service | リソースがサポートする製品、アプリケーション、サービスを識別します。 |
| 役割 | sql, web, messaging | リソースに関連付けられた役割を識別します。 |

会社や製品に固有の命名規約を作成する場合、接頭辞と接尾辞の共通セットと、それらの位置（接頭辞か接尾辞か）を選ぶことが重要です。

## 命名ルールと制約事項

Azure においては、リソースまたはサービスの種類ごとに、命名の制約事項とスコープのセットが強制されます。
あらゆる命名規約と命名パターンは、これら必須の命名ルールとスコープに従わなければなりません。
たとえば、VM の名前は DNS 名に対応している（つまり Azure 全体で一意である必要がある）一方で、VNET の名前は、VNET が作成されたリソース グループのスコープになります。


一般的に、どんな名前でも、その先頭と末尾文字に特殊文字（`-` や `_`）を使わないようにします。ほとんどの検証ルールでこれらの文字がはじかれるためです。

| 分類 | サービスまたはエンティティ | スコープ | 長さ | 大文字小文字 | 使用可能な文字 | 推奨パターン | 例 |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| リソースグループ | リソースグループ | グローバル | 1-64 | 区別しない | 英数字、アンダースコア、ハイフン | `<サービスの短い名前>-<環境>-rg` | `profx-prod-rg` |
| リソースグループ | 可用性セット | リソースグループ | 1-80 | 区別しない | 英数字、アンダースコア、ハイフン | `<サービスの短い名前>-<文脈>-as` | `profx-sql-as` |
| 一般 | タグ | 関連付けられたエンティティ | 512 (name), 256 (value) | 区別しない | 英数字 | `"key" : "value"` | `"department" : "Central IT"` |
| 計算 | 仮想マシン | リソースグループ | 1-15 | 区別しない | 英数字、アンダースコア、ハイフン | `<名前>-<役割>-<インスタンス>` | `profx-sql-001` |
| ストレージ | ストレージアカウント名（データ） | グローバル | 3-24 | 小文字のみ | 英数字 | `<サービスの短い名前><種類><数字>` | `profxdata001` |
| ストレージ | ストレージアカウント名（ディスク） | グローバル | 3-24 | 小文字のみ | 英数字 | `<VM 名からハイフンを除いたもの>st<数字>` | `profxsql001st0` |
| ストレージ | コンテナーの名前 | ストレージアカウント | 3-63 |	小文字のみ | 英数字とハイフン | `<文脈>` | `logs` |
| ストレージ | Blob の名前 | コンテナー | 1-1024 | 区別する | URL に使える任意の文字 | `<blob の使い方次第>` | `<blob の使い方次第>` |
| ストレージ | Queue の名前 | ストレージアカウント | 3-63 | 小文字のみ | 英数字とハイフン | `<サービスの短い名前>-<文脈>-<数字>` | `awesomeservice-messages-001` |
| ストレージ | Table の名前 | ストレージアカウント | 3-63 | 区別しない | 英数字 | `<サービスの短い名前>-<文脈>` | `awesomeservice-logs` |
| ストレージ | File の名前 | ストレージアカウント | 3-63 | 小文字のみ | 英数字 | `<blob の使い方次第>` | `<blob の使い方次第>` |
| ネットワーク | 仮想ネットワーク（VNet） | リソースグループ | 2-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `<サービスの短い名前>-[セクション]-vnet` | `profx-vnet` |
| ネットワーク | サブネット | 親 VNet | 2-80 | 区別しない | 英数字、アンダースコア、ハイフン、ピリオド | `<役割>-subnet` | `gateway-subnet` |
| ネットワーク | ネットワーク インターフェイス | リソースグループ | 1-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `<VM 名>-<数字>nic` | `profx-sql1-1nic` |
| ネットワーク | ネットワーク セキュリティ グループ | リソースグループ | 1-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `<サービスの短い名前>-<文脈y>-nsg` | `profx-app-nsg` |
| ネットワーク | ネットワーク セキュリティ グループ 規則 | リソースグループ | 1-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `<説明的な文脈>` | `sql-allow` |
| ネットワーク | パブリック IP アドレス | リソースグループ | 1-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `<VM またはサービスの名前>-pip` | `profx-sql1-pip` |
| ネットワーク | ロードバランサー | リソースグループ | 1-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `<サービスまたは役割>-lb` | `profx-lb` |
| ネットワーク | ロードバランサー 規則の構成 | ロードバランサー | 1-80 | 区別しない | 英数字、ハイフン、アンダースコア、ピリオド | `説明的な文脈` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## タグを使用してリソースを編成する

Azure リソースマネージャーは、文脈の特定と、自動化された操作の整備のための、任意のテキスト文字列によるエンティティへのタグ付けをサポートします。
たとえば、`"sqlVersion: "sql2014ee"` というタグは、そのタグが付けられたエンティティに対して自動化されたスクリプトを実行する場合に、
SQL Server 2014 Enterprise Edition を実行するデプロイメントの VM であることを特定可能にします。  
タグは、選択した命名規約に沿った文脈を補い、拡張するために使用すべきです。

> [AZURE.TIP] タグの優れた点をもう 1 つ挙げると、タグはリソースグループをまたがって付くので、全く別のデプロイメント間でエンティティをリンクさせて関連付けることができます。

各リソースまたはリソースグループは、最大で **15 個** のタグを付けることができます。タグの名前は 512 文字までで、タグの値は 256 文字までです。

リソースのタグ付けの詳細情報については、[Using tags to organize your Azure resources](../resource-group-using-tags.md) を参照してください。

一般的なタグ付けのユースケースを抜粋します：

- **請求**。請求またはチャージバックコードによってリソースをグループ化し、関連付ける。
- **サービスの文脈の識別**。共通のオペレーションとグループ化のための、リソースグループをまたがったリソースのグループを識別する。
- **アクセス制御とセキュリティの文脈**。ポートフォリオ、システム、サービス、アプリ、インスタンス等に基づいた、管理者の役割の識別。

> [AZURE.TIP] 早く、頻繁にタグを付けてください。その場でベースラインとなるタグ付けのスキームを持ち、常に調整していく方が、事後に改善するよりも優れています。

一般的なタグ付けの方法の例を抜粋します：

<!--
    Translatation note:
    There are no direct words for DRI in Japanese.
    I think '責任' does not equal to responsibility, it also include accountability.
    And '担当者' are often responsible for complete their jobs including trouble resolution, so I choose '担当者' here.
-->

| タグの名前 | キー | 例 | コメント |
| -------- | --- | ------- | ------- |
| 請求先／内部チャージバック ID | billTo  | `IT-Chargeback-1234` | 内部投入産出または請求書のコード |
| オペレーターまたは担当者（DRI：Directly Responsible Individual） | managedBy | `joe@contoso.com`  | 別名または電子メールアドレス |
| プロジェクト名 | project-name | `myproject`  | プロジェクトまたは製品ラインの名前 |
| プロジェクトバージョン | project-version | `3.4`  | プロジェクトまたは製品ラインのバージョン |
| 環境 | environment | `<Production, Staging, QA >` | 環境 ID | 
| 層 | tier | `Front End, Back End, Data` | 層（ティア）または役割／文脈を識別するもの |
| データプロファイル | dataProfile | `Public, Confidential, Restricted, Internal` | リソースに保存されたデータの機密度 |
 
## Tips and Tricks

リソースの種類によっては、命名と規約について追加の注意を必要とすることがあります。

### 仮想マシン

とりわけ大規模なトポロジーにおいて、仮想マシンの命名を注意深く行うことで、各マシンの役割と目的の特定が簡単になり、スクリプト処理をよりわかりやすくできます。

> [AZURE.WARNING] Azure における各仮想マシンは、Azure リソース名と、オペレーティングシステムとしてのホスト名の両方を持ちます。  
> リソース名とホスト名が異なると VM の管理が困難になる可能性があるので、そのようなことは避けるべきです。
> たとえば、既にホスト名を指定した、構成済みのオペレーティングシステムを含む .vhd から、仮想マシンを作成する場合です。
 
- [Naming conventions for Windows Server VMs](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### ストレージアカウントとストレージエンティティ

ストレージアカウントの主なユースケースは 2 つあります。VM が動作するためのディスク用と、blob や queue や table へのデータ保存用です。
VM ディスク用のストレージアカウントは、親 VM 名に関連する命名規約に従うべきです
（ハイエンドの VM SKU では複数のストレージアカウントが必要になる可能性があり、その場合は数字の接尾辞を付けます）。


> [AZURE.TIP] ストレージアカウントは、データ用かディスク用かにかかわらず、複数のストレージアカウントを活用できるような
> 命名規約に従うべきです（つまり、常に数字の接尾辞を使うべきです）。

Azure ストレージアカウントにある blob データへのアクセスに、カスタムドメイン名を使うように構成できます。
Blob サービス用の既定のエンドポイントは `https://mystorage.blob.core.windows.net` です。

ただし、ストレージアカウント用の blob エンドポイントにカスタムドメイン（たとえば www.contoso.com）を対応付ける場合、
そのドメイン名を使用してストレージアカウント内の blob データにアクセスすることもできます。
たとえば、カスタムドメイン名を使うと、`http://www.contoso.com/mycontainer/myblob` で 
`http://mystorage.blob.core.windows.net/mycontainer/myblob` にアクセスできます。


この機能の詳細な構成方法については、[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](../storage/storage-custom-domain-name.md) を参照してください。

blob、コンテナ、table の命名の詳細情報については、以下も参照してください：

<!--
    Translation note:
    Machine translatation often cause troubles, so it is better to point English site here. 
    It might be good to point translated site when the sites can accept community feedback to improve translations.
-->
- [Naming and Referencing Containers, Blobs, and Metadata](https://msdn.microsoft.com/en-us/library/dd135715.aspx)
- [Naming Queues and Metadata](https://msdn.microsoft.com/en-us/library/dd179349.aspx)
- [Naming Tables](https://msdn.microsoft.com/en-us/library/azure/dd179338.aspx)

blob 名には任意の文字の組み合わせを指定できますが、予約済みの URL 文字は正しくエスケープしなければなりません。
blob には、ピリオド（.）もしくはスラッシュ（/）で終わる名前、またはこれら 2 つの組み合わせによる名前を付けないでください。
規約により、スラッシュは **仮想** ディレクトリの区切り文字です。
blob 名にバックスラッシュ（\、円記号）を使わないでください。
クライアント API でバックスラッシュを許可していたとしても、ハッシュ計算を正しく行うことができず、署名が一致しなくなります。

作成後にストレージアカウントやコンテナの名前を修正することはできません。
新しい名前を使いたい場合には、一度削除して新しく作成しなおさなければなりません。

> [AZURE.TIP] 新しいサービスやアプリケーションの開発を始める前に、全てのストレージアカウントと種類に対して命名規約を定めることを推奨します。

## 例 - n 層サービスのデプロイ

この例では、フロントエンドの IIS サーバー（Windows Server VM でホストされます）、SQL Server（2 台の Windows Server VM でホストされます）、
ElasticSearch クラスター（6 台の Linux VM でホストされます）、およびそれらに関連付けられたストレージアカウント、
仮想ネットワーク、リソースグループ、ロードバランサーで構成された、n 層サービスの構成を定義します。


まず、このアプリケーションの文脈依存の規約を定義します：

| エンティティ | 規約 | 説明  |
| ------ | ---------- | ------------ |  
| サービス名 | `profx` | デプロイするアプリケーションまたはサービスの短い名前 |
| 環境 | `prod` | これは本番デプロイ用です（QA やテスト用等ではありません） |

前述のベースラインから、リソース種別ごとの規約に対応付けることができます：

| リソース種別 | 規約のベース | 例 |
| ------------- | --------------- | ------- |
| サブスクリプション | `<会社名> <部署名（省略可能）> <製品ライン（省略可能）> <環境>` | `Contoso IT InternalApps Profx Production` |
| リソースグループ | `<サービス名>-rg` | `profx-rg` |
| 仮想ネットワーク | `<サービス名>-vnet` | `profx-vnet` |
| サブネット | `<役割>-subnet` | `sql-vnet` |
| ロードバランサー | `<サービス名>-lb` | `profx-lb` |
| 仮想マシン | `<サービス名>-<役割>[<数字>]` | `profx-sql0` |
| ストレージアカウント | `<VM 名からハイフンを除いたもの>st<数字>` | `profxsql0st0` |

図で表すと以下のようになります：

![application topology diagram](media/guidance-naming-conventions/guidance-naming-convention-example.png "Sample Application Topology")

## サンプル - 上記のサンプルをデプロイするための Azure CLI スクリプト

<!--
    Translation note:
    * 'list or URN's' in comment of setting-up LINUX_BASE_IMAGE variable should be 'list of URN's'.
-->
```bash
#!/bin/sh

#####################################################################
# 命名規約の説明用のアプリケーションの構築用に Azure CLI を使用するサンプルスクリプト。
#
# 注：このスクリプトは本番環境用に記述したものではないので、
# 可用性セットの作成や、ネットワーク セキュリティグループの構成などは行いません。
#####################################################################

# クラスターをデプロイするために命名規約を構築するための変数群を設定する
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# アプリケーション内の項目に関連付けるタグを設定する
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# どのサブスクリプションがアクティブ／既定なのかについて混乱しないように、
# 明示的にサブスクリプションを設定する
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# 推奨された規約を使用して、エンティティの名前を設定する
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# ほとんどの CLI コマンドに指定する postfix 変数を設定する
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Linux と Windows のイメージ用の VM 規約を設定

# Windows の場合、
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
# で URN のリストを取得
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# Linux の場合、
# azure vm image list ${LOCATION} canonical ubuntuserver
# で URN のリストを取得
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## 関数定義
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # この VM 用のネットワーク インターフェイス カードを作成
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # この VM のディスク（プレミアムローカル冗長ストレージ -> PLRS）用のストレージアカウントを作成
    # 注：${var//-/} 構文で VM 名からハイフンを取り除いている
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # 診断ストレージアカウントの名前をブート診断用の blob URI に対応付ける
    # これは、（現在のところ）名前付きプレミアムストレージアカウントをデプロイするときに必須
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # VM を作成
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# リソースの作成

# ステップ 1 - 所属させるリソースグループを作成
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# ステップ 2 - ネットワーク セキュリティ グループを作成

# ステップ 3 - ネットワーク（VNet とサブネット）を作成
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# ステップ 4 - ロードバランサーとネットワーク セキュリティ規則を定義
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# 本番デプロイ用のスクリプトでは、ここでロードバランサーの規則とネットワーク セキュリティ グループを作成すること

# ステップ 5 - 診断用ストレージアカウントを作成
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# ステップ 6.1 - ゲートウェイ VM 群の作成
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# ステップ 6.2 - ElasticSearch のマスター VM 群とデータ VM 群を作成
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# ステップ 6.3 - SQL VM 群を作成
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```