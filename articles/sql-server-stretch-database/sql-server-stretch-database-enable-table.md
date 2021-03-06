<properties
	pageTitle="デーブルの Stretch Database を有効にする | Microsoft Azure"
	description="Stretch Database のテーブルを設定する方法について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# テーブルの Stretch Database を有効にする

Stretch Database のテーブルを設定するには、SQL Server Management Studio でテーブルの **[Stretch]、[有効化]** の順に選択し、**[Stretch テーブルを有効にする]** ウィザードを起動します。Transact-SQL を利用し、既存のテーブルで Stretch Database を有効にしたり、新しいテーブルを作成して Stretch Database を有効にしたりすることもできます。

-   別個のテーブルに履歴データを保存する場合、テーブル全体を移行できます。

-   テーブルに過去と現在の両方のデータが含まれている場合、移行する行を選択するフィルター述語を指定できます。

**前提条件**。テーブルに **[Stretch]、[有効化]** を選択したとき、データベースの Stretch Database を有効にしていない場合、ウィザードにより Stretch Database のデータベースが最初に設定されます。このトピックの手順ではなく、「[[Enable Database for Stretch (Stretch Database を有効にする)] ウィザードを実行する方法の概要](sql-server-stretch-database-wizard.md)」の手順に従ってください。

**アクセス許可**。データベースまたはテーブルで Stretch Database を有効にするには、db\_owner アクセス許可が必要です。テーブルで Stretch Database を有効にするには、テーブルの ALTER アクセス許可も必要です。

## <a name="EnableWizardTable"></a>ウィザードを使用し、テーブルで Stretch Database を有効にする
**ウィザードを起動する**

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch を有効にするテーブルを選択します。

2.  右クリックして **[Stretch]** を選択し、**[有効化]** を選択してウィザードを起動します。

**はじめに**

ウィザードの目的と前提条件を確認します。

**データベース テーブルを選択する**

有効にするテーブルが表示され、選択されていることを確認します。

テーブル全体を移行することも、ウィザードで単純なフィルター述語を指定することもできます。別の種類のフィルター述語を使用して、移行する行を選択する場合は、次のいずれかの操作を行います。

-   ウィザードを終了し、ALTER TABLE ステートメントを実行してテーブルの Stretch を有効にし、述語を指定します。

-   ウィザードを終了してから、ALTER TABLE ステートメントを実行して、述語を指定します。必要な手順については、「[Add a filter predicate after running the Wizard (ウィザードの実行後にフィルター述語を追加する)](sql-server-stretch-database-predicate-function.md#addafterwiz)」をご覧ください。

ALTER TABLE 構文については、このトピックの後の方で説明しています。

**まとめ**

入力した値とウィザードで選択したオプションを確認します。**[完了]** を選択して、Stretch を有効にします。

**結果**

結果を確認します。

## <a name="EnableTSQLTable"></a>Transact-SQL を使用し、テーブルで Stretch Database を有効にする
Transact-SQL を利用し、既存のテーブルで Stretch Database を有効にしたり、新しいテーブルを作成して Stretch Database を有効にしたりできます。

### オプション
CREATE TABLE または ALTER TABLE を実行し、テーブルで Stretch Database を有効にするとき、次のオプションを利用します。

-   テーブルに履歴データと現行データの両方が含まれている場合、`FILTER_PREDICATE = <predicate>` 句を使用し、移行する行を選択する述語を指定することもできます。述語では、インライン テーブル値関数を呼び出す必要があります。詳細については、[移行する行の選択におけるフィルター述語の使用](sql-server-stretch-database-predicate-function.md)に関するページをご覧ください。フィルター述語を指定しない場合、テーブル全体が移行されます。

    >   [AZURE.NOTE] 指定したフィルター述語のパフォーマンスが悪いと、データ移行のパフォーマンスも悪くなります。Stretch Database は CROSS APPLY 演算子を利用し、テーブルにフィルター述語を適用します。

-   データの移行をすぐに開始する場合は `MIGRATION_STATE = OUTBOUND` を指定し、データの移行の開始を先送りする場合は `MIGRATION_STATE = PAUSED` を指定します。

### 既存テーブルの Stretch Database を有効にする
既存テーブルを Stretch Database 用に設定するには、ALTER TABLE コマンドを実行します。

テーブル全体を移行し、データ移行をすぐに開始する例を次に示します。

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
`dbo.fn_stretchpredicate` インライン テーブル値関数で特定される行のみを移行し、データ移行を先送りする例は次のようになります。フィルター述語の詳細については、[移行する行の選択におけるフィルター述語の使用](sql-server-stretch-database-predicate-function.md)に関するページをご覧ください。

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

詳細については、「[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)」をご覧ください。

### 新しいテーブルを作成し、Stretch Database を有効にする
新しいテーブルを作成し、Stretch Database を有効にするには、CREATE TABLE コマンドを実行します。

テーブル全体を移行し、データ移行をすぐに開始する例を次に示します。

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
`dbo.fn_stretchpredicate` インライン テーブル値関数で特定される行のみを移行し、データ移行を先送りする例は次のようになります。フィルター述語の詳細については、[移行する行の選択におけるフィルター述語の使用](sql-server-stretch-database-predicate-function.md)に関するページをご覧ください。

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

詳細については、「[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)」をご覧ください。


## 関連項目

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0608_2016-->