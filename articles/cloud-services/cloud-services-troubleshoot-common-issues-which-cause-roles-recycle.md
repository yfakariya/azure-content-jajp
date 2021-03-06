<properties
   pageTitle="クラウド サービス ロールがリサイクルされる一般的な原因 | Microsoft Azure"
   description="クラウド サービス ロールが突然リサイクルされることで、重大なダウンタイムが生じることがあります。ロールのリサイクルを引き起こす一般的な問題とダウンタイムの改善策を取り上げました。"
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="06/03/2016"
   ms.author="v-six" />

# ロールのリサイクルを引き起こす一般的な問題

この記事では、デプロイメントに伴う問題の一般的な原因と問題解決に役立つトラブルシューティングのヒントを紹介します。アプリケーションに存在する問題の兆候として、ロール インスタンスが起動に失敗したり、初期化、ビジー、停止という状態を繰り返したりすることが挙げられます。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## ランタイムの依存コンポーネントの不足

アプリケーションのロールに必要なアセンブリが .NET Framework や Azure マネージ ライブラリに含まれていない場合、そのアセンブリをアプリケーション パッケージに明示的にインクルードする必要があります。それ以外の Microsoft フレームワークは、明示的に指定しない限り、Azure では利用できないのでご注意ください。そのようなフレームワークにロールが依存している場合、対象となるアセンブリをアプリケーション パッケージに追加する必要があります。

アプリケーションをビルドしてパッケージ化する前に、次のことを確認します。

- Visual studio を使用している場合、Azure SDK や .NET Framework に含まれていない、プロジェクトのすべての参照アセンブリの **[ローカル コピー]** プロパティが **[True]** に設定されていること。

- web.config ファイルの compilation 要素が、未使用のアセンブリを参照していないこと。

- すべての .cshtml ファイルの **[ビルド アクション]** が **[コンテンツ]** に設定されていること。この設定によって当該ファイルが正しくパッケージに追加され、他の参照ファイルをパッケージに追加することができます。

## アセンブリのターゲット プラットフォームの間違い

Azure は 64 ビット環境です。そのため、32 ビット ターゲット用にコンパイルされた .NET アセンブリは Azure では動作しません。

## 初期化中または停止中ハンドルされない例外をロールがスローする

[RoleEntryPoint] クラスのメソッド ([OnStart]、[OnStop]、[Run] を含む) によってスローされる例外はすべて、ハンドルされない例外となります。そのいずれかのメソッドでハンドルされない例外が発生した場合、ロールはリサイクルされます。ロールが何度もリサイクルされる場合、起動の途中でハンドルされない例外がスローされている可能性があります。

## Run メソッドが終了している

[Run] メソッドは無限に実行するように設計されています。[Run] メソッドをオーバーライドする場合は、無期限にスリープ状態となるようにコードを記述する必要があります。[Run] メソッドから制御が戻った場合、ロールはリサイクルされます。

## DiagnosticsConnectionString の設定に誤りがある

アプリケーションで Azure 診断を使用している場合、サービス構成ファイルで `DiagnosticsConnectionString` 構成設定を指定する必要があります。この設定で、Azure にある該当ストレージ アカウントへの HTTPS 接続を指定します。

アプリケーション パッケージを Azure にデプロイする前に `DiagnosticsConnectionString` が正しく設定されていることを確かめるために、次の点を確認してください。

- Azure 内の有効なストレージ アカウントが `DiagnosticsConnectionString` の設定に指定されていること。既定では、エミュレートされたストレージ アカウントが指定されているため、アプリケーション パッケージをデプロイする前に、この設定を明示的に変更する必要があります。この設定を変更しなかった場合、ロール インスタンスが診断モニターを起動しようとしたときに例外がスローされます。ロール インスタンスが無限にリサイクルされる原因となる場合があります。

- 接続文字列は次の[形式](../storage/storage-configure-connection-string.md)で指定します。(プロトコルには HTTPS を指定する必要があります。) *MyAccountName* には該当するストレージ アカウントの名前を、*MyAccountKey* には該当するアクセス キーを指定してください。

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Azure Tools for Microsoft Visual Studio をアプリケーションの開発に使用している場合、この値は、[プロパティ ページ](https://msdn.microsoft.com/library/ee405486)を使用して設定できます。

## エクスポートした証明書に秘密キーが含まれていない

Web ロールを SSL で実行する場合は、エクスポートした管理証明書に秘密キーが含まれていることを確認してください。*Windows 証明書マネージャー*を使用して証明書をエクスポートする場合は必ず、**[Export the private key (秘密キーをエクスポートします)]** オプションに **[はい]** を選択してください。証明書は PFX 形式でエクスポートする必要があります。現在サポートされている形式はこれだけです。

## 次のステップ

クラウド サービスの他の[トラブルシューティングに関する記事](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services)を参照します。

他のロール リサイクル シナリオを確認するには、[Kevin Williamson によるブログ シリーズ](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)をご覧ください。




[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

<!---HONumber=AcomDC_0608_2016-->