<properties 
   pageTitle="アプリケーションのアップグレード中における SQL Database のビジネス継続性" 
   description="このセクションでは、アプリケーションのアップグレード中にダウンタイムを防ぐためのガイダンスを説明します。" 
   services="sql-database"
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="elfish"/>

#ダウンタイムのないアプリケーションのアップグレード

Microsoft Azure では、「アプリケーション」という用語はフロント エンドなどのコンポーネント、クラウド サービスでデプロイされるサービス、アプリケーション データやメタデータを保持するために使用するデータ層を参照します。クラウド アプリケーションは 24 時間 365 日継続してサービスを提供することを目的にしていることがほとんどです。データ層における変更がライブ サイトで適用される際にアプリケーションの新しいバージョンがデプロイされることで、利用できる機能の数が減ったり、完全なダウンタイムになるなど、なんらかの障害が発生する場合があります。

アプリケーションのアップグレード プロセスを設計する場合、その主な目的は、アプリケーション機能が減少する時間を排除するか、大幅に減らすことであるべきです。そのためには、アップグレード時の障害に備えたバックアップとして使用するアプリケーションの一時コピーを作成するようにします。アップグレードを設計・計画するときには、次の要因を考慮してください。

1.	アプリケーションの機能が制限される最大許容時間 
2.	アップグレード プロセス時に使用できる最低限の機能
3.	アップグレード中にエラーが発生した場合におけるロールバック権限
4.	関係する合計コストこれには一時コピー (アクティブ geo レプリケーション用のその他のデータベースなど) を作成するために必要なアプリケーション コンポーネントの追加コストや、アップグレード プロセスで使用する一時的なデプロイの増分コストが含まれます。 

アプリケーションを読み取り専用モードで一時的に操作できる場合、ダウンタイムを完全に回避するようにアップグレードのワークフローを効果的に設計できます。特定のアプリケーション トポロジでアップグレードのワークフローを実装する方法を理解するには、「[SQL Database アクティブ geo レプリケーションを使用したクラウド アプリケーションのローリング アップグレードの管理](sql-database-manage-application-rolling-upgrade.md)」をご覧ください。
 
 
 

<!---HONumber=AcomDC_0608_2016-->