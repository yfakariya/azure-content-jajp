<properties 
	pageTitle="Azure Redis Cache でデータをインポートまたはエクスポートする | Microsoft Azure" 
	description="Premium Azure Redis Cache インスタンスを使って Blob Storage との間でデータのインポートとエクスポートを行う方法について説明します。" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/23/2016" 
	ms.author="sdanie"/>

# Azure Redis Cache でデータをインポートまたはエクスポートする

Import/Export は Azure Redis Cache のデータ管理操作です。Redis Cache データベース (RDB) のスナップショットを Premium キャッシュからエクスポートし、Azure ストレージ アカウント内のページ BLOB にインポートすることで、Azure Redis Cache との間でデータのインポートとエクスポートを実行できます。これにより、異なる Azure Redis Cache インスタンス間での移行または使用前のキャッシュへのデータ入力が可能になります。

この記事では Azure Redis Cache でデータをインポートまたはエクスポートする方法を説明し、よく寄せられる質問に回答します。

>[AZURE.IMPORTANT] Import/Export はプレビュー版ですので、[Premium レベル](cache-premium-tier-intro.md) キャッシュにのみ使用可能です。

## インポート

インポートは、任意のクラウドまたは環境で稼働している任意の Redis サーバー (Linux や Windows のほか、アマゾン ウェブ サービスをはじめとする各種クラウド プロバイダーで稼働している Redis など) から Redis と互換性のある RDB ファイルを取り込むときに使用できます。データをインポートすると、あらかじめデータが入力されたキャッシュを簡単に作成できます。インポート処理中に、Azure Redis Cache は RDB ファイルを Azure ストレージからメモリに読み込み、キーをキャッシュに挿入します。

>[AZURE.NOTE] インポート操作を開始する前に、リージョンとサブスクリプションが Azure Redis Cache インスタンスと同じである Azure Storage 内のページ BLOB に Redis データベース (RDB) ファイルがアップロードされる設定になっていることを確認してください。詳細については、[Azure Blob Storage の使用](../storage/storage-dotnet-how-to-use-blobs.md)に関するページをご覧ください。[Azure Redis Cache のエクスポート](#export)機能を使って RDB ファイルをエクスポートした場合、そのファイルは既にページ BLOB に格納されており、インポートの準備ができています。

1. エクスポートされた 1 つ以上のキャッシュ BLOB をインポートするには、Azure ポータルで[キャッシュを参照](cache-configure.md#configure-redis-cache-settings)し、キャッシュ インスタンスの **[設定]** ブレードで **[データのインポート]** をクリックします。

    ![Import data][cache-import-data]

2. **[BLOB の選択]** をクリックして、インポートするデータを含むストレージ アカウントを選択します。

    ![ストレージ アカウントの選択][cache-import-choose-storage-account]

3. インポートするデータが含まれているコンテナーをクリックします。

    ![Choose container][cache-import-choose-container]

4. BLOB 名の左側の領域をクリックして、インポートする 1 つ以上の BLOB を選択し、**[選択]** をクリックします。

    ![Choose blobs][cache-import-choose-blobs]

5. **[インポート]** をクリックしてインポート処理を開始します。

    >[AZURE.IMPORTANT] インポート処理中は、キャッシュ クライアントからキャッシュにアクセスすることはできません。また、キャッシュ内の既存データはすべて削除されます。

    ![インポート][cache-import-blobs]

    インポート操作の進行状況を監視するには、Azure ポータルからの通知を確認するか、[監査ログ](cache-configure.md#support-amp-troubleshooting-settings)のイベントを確認します。

    ![Import progress][cache-import-data-import-complete]


## エクスポート

エクスポート機能では、Azure Redis Cache に格納されたデータを Redis と互換性のある RDB ファイルにエクスポートできます。この機能を使えば、Azure Redis Cache インスタンス間でデータを移動したり、Azure Redis Cache インスタンスから別の Redis サーバーにデータを移動したりできます。エクスポート処理中に、Azure Redis Cache サーバー インスタンスをホストする VM 上に一時ファイルが作成され、それが指定されたストレージ アカウントにアップロードされます。エクスポート操作が完了したら、操作の成否にかかわらず、この一時ファイルは削除されます。

1. キャッシュの現在の内容をストレージにエクスポートするには、Azure ポータルで[キャッシュを参照](cache-configure.md#configure-redis-cache-settings)し、キャッシュ インスタンスの **[設定]** ブレードで **[データのエクスポート]** をクリックします。

    ![Choose storage container][cache-export-data-choose-storage-container]

2. **[ストレージ コンテナーの選択]** をクリックし、目的のストレージ アカウントを選択します。ストレージ アカウントは、キャッシュと同じサブスクリプションおよびリージョン内にある必要があります。

    >[AZURE.IMPORTANT] Import/Export が機能するページ BLOB はクラシック ストレージ アカウントと ARM ストレージ アカウントの両方でサポートされていますが、現時点では [Blob Storage アカウント](../storage/storage-blob-storage-tiers.md#blob-storage-accounts)ではサポートされていません。

    ![ストレージ アカウント][cache-export-data-choose-account]

3. 目的の BLOB コンテナーを選び、**[選択]** をクリックします。新しいコンテナーを使用するには、まず **[コンテナーの追加]** をクリックして新しいコンテナーを追加したうえで、一覧からそれを選択します。

    ![Choose storage container][cache-export-data-container]

4. **BLOB 名のプレフィックス**を入力し、**[エクスポート]** をクリックしてエクスポート処理を開始します。BLOB 名のプレフィックスは、このエクスポート操作によって生成されるファイル名のプレフィックスとして使用されます。

    ![エクスポート][cache-export-data]

    エクスポート操作の進行状況を監視するには、Azure ポータルからの通知を確認するか、[監査ログ](cache-configure.md#support-amp-troubleshooting-settings)のイベントを確認します。

    ![][cache-export-data-export-complete]

    エクスポート処理中でも、キャッシュは使用可能な状態のままですので注意してください。


## Import/Export の FAQ

このセクションでは、Import/Export 機能についてよく寄せられる質問を掲載しています。

-	[Import/Export はどの価格レベルで使用できますか?](#what-pricing-tiers-can-use-importexport)
-	[どの Redis サーバーからでもデータをインポートできるのでしょうか?](#can-i-import-data-from-any-redis-server)
-	[Import/Export 操作中にキャッシュを使用することはできますか?](#will-my-cache-be-available-during-an-importexport-operation)
-	[Redis クラスターで Import/Export を使うことはできますか?](#can-i-use-importexport-with-redis-cluster)
-	[Import/Export がカスタム データベース設定を操作する方法](#how-does-importexport-work-with-a-custom-databases-setting)
-	[Import/Export と Redis の永続化にはどのような違いがありますか?](#how-is-importexport-different-from-redis-persistence)
-	[PowerShell、CLI、またはその他の管理クライアントを使って Import/Export を自動化することはできますか?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-	[Import/Export 操作中にタイムアウト エラーが発生しました。これはどういうことですか?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-	[Azure Blob Storage にデータをエクスポートしているときにエラーが発生しました。なぜでしょうか?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### Import/Export はどの価格レベルで使用できますか?

Import/Export は Premium 価格レベルでのみ使用できます。

### どの Redis サーバーからでもデータをインポートできるのでしょうか?

はい。インポートできるのは Azure Redis Cache インスタンスからエクスポートされたデータだけではありません。任意のクラウドや環境 (Linux や Windows のほか、アマゾン ウェブ サービスなどの各種クラウド プロバイダー) で稼働している任意の Redis サーバーから RDB ファイルをインポートできます。これを行うには、RDB ファイルを目的の Redis サーバーから Azure ストレージ アカウント内のページ BLOB にアップロードし、さらに Premium Azure Redis Cache インスタンスにインポートします。具体的には、運用環境のキャッシュからデータをエクスポートし、テストまたは移行のためのステージング環境の一部として使用するキャッシュにインポートする場合が考えられます。

### Import/Export 操作中にキャッシュを使用することはできますか?

-	**エクスポート** - キャッシュは使用可能な状態のままです。エクスポート操作中もキャッシュの使用を続行できます。
-	**インポート** - インポート処理が開始されると、キャッシュは使用できなくなります。インポート処理が終了すると、キャッシュは使用可能になります。


### Redis クラスターで Import/Export を使うことはできますか?

はい。さらに、クラスター化されたキャッシュとクラスター化されていないキャッシュの間でインポート/エクスポートを実行することもできます。Redis クラスターは、[データベース 0 のみをサポートする](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)ため、0 以外のデータベースのデータはインポートされません。クラスター化されたキャッシュのデータがインポートされると、クラスターのシャード間でキーが再配分されます。

### Import/Export がカスタム データベース設定を操作する方法

価格レベルによってさまざまな[データベースの制限](cache-configure.md#databases)があるため、キャッシュの作成中に `databases` の設定にカスタム値を設定する場合、インポート時の考慮事項がいくつかあります。

-	エクスポートしたレベルより低い `databases` の制限の価格レベルにインポートする場合:
	-	すべての価格レベルが 16 の `databases` の既定の数を使用している場合、データは失われません。
	-	インポートしているレベルの制限範囲に含まれるユーザー設定の数値の `databases` を使用している場合、データは失われません。
	-	エクスポートしたデータが新しいレベルの制限を超えるデータベースのデータを含む場合、レベルの高いデータベースのデータはインポートされません。

### Import/Export と Redis の永続化にはどのような違いがありますか?

Azure Redis Cache 永続化では、Redis に保管されたデータを Azure Storage で永続化することができます。永続化を構成すると、Azure Redis Cache は、Redis Cache のスナップショットを構成可能なバックアップ頻度に基づいて Redis バイナリ形式でディスクに保存します。プライマリ キャッシュとレプリカ キャッシュの両方が無効になるような致命的なイベントが発生した場合、最新のスナップショットを使用してキャッシュ データが自動的に復元されます。詳細については、「[Premium Azure Redis Cache のデータ永続化の構成方法](cache-how-to-premium-persistence.md)」をご覧ください。

Import/Export では、Azure Redis Cache へのデータの取り込みと Azure Redis Cache からのデータのエクスポートが可能です。Redis 永続化を使用してバックアップと復元が構成されることはありません。


### PowerShell、CLI、またはその他の管理クライアントを使って Import/Export を自動化することはできますか?

この機能はプレビュー期間中は使用できませんが、近日対応予定です。

### Import/Export 操作中にタイムアウト エラーが発生しました。これはどういうことですか?

**[データのインポート]** ブレードまたは **[データのエクスポート]** ブレードに操作を開始しないまま留まっている時間が 15 分を超えると、次のようなエラーが発生します。

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

これを解決するには、インポート操作またはエクスポート操作を 15 分以内に開始してください。

### Azure Blob Storage にデータをエクスポートしているときにエラーが発生しました。なぜでしょうか?

Import/Export は、ページ BLOB として格納されている RDB ファイルでのみ機能します。それ以外のタイプの BLOB は、クール層またはホット層の Blob Storage アカウントも含め、現時点ではサポートされていません。

    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png

<!---HONumber=AcomDC_0525_2016-->