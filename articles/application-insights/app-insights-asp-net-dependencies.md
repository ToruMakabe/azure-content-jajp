<properties 
	pageTitle="Application Insights での依存関係の追跡" 
	description="オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2016" 
	ms.author="awills"/>


# Application Insights の設定: 依存関係の追跡


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



*依存関係*は、アプリによって呼び出される外部コンポーネントです。一般的には、HTTP を使用して呼び出されるサービス、またはデータベース、あるいはファイル システムです。Visual Studio Application Insights では、アプリケーションが依存関係を待機する期間や、依存関係の呼び出しが失敗する頻度を簡単に確認できます。

![サンプルのグラフ](./media/app-insights-asp-net-dependencies/10-intro.png)

すぐに使用できる依存関係モニターは現在、次の種類の依存関係の呼び出しを報告します。

* ASP.NET
 * SQL データベース
 * HTTP ベースのバインドを使用する ASP.NET Web および WCF サービス
 * ローカルまたはリモートの HTTP 呼び出し
 * Azure DocumentDb、テーブル、Blob Storage、およびキュー
* Java
 * MySQL、SQL Server、PostgreSQL、SQLite などの [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) ドライバーを使用したデータベースの呼び出し
* Web ページの JavaScript - [Web ページの SDK](app-insights-javascript.md) では、AJAX 呼び出しを依存関係として自動的にログに記録します。

[[TrackDependency API]](app-insights-api-custom-events-metrics.md#track-dependency) を使用して他の依存関係を監視するために、独自の SDK 呼び出しを記述することができます。


## 依存関係の監視をセットアップするには

[Microsoft Azure](http://azure.com) サブスクリプションが必要です。

### アプリが IIS サーバーで実行されている場合

Web アプリが .NET 4.6 以降で実行されている場合は、アプリに [Application Insights SDK をインストール](app-insights-asp-net.md)するか、Application Insights Status Monitor をインストールすることができます。両方をインストールする必要はありません。

それ以外の場合、サーバーに Application Insights Status Monitor をインストールしてください。

1. IIS Web サーバーで、管理者の資格情報を使用してログインします。
2. [Status Monitor インストーラー](http://go.microsoft.com/fwlink/?LinkId=506648)をダウンロードし、実行します。
4. インストール ウィザードで、Microsoft Azure にサインインします。

    ![Microsoft アカウントの資格情報で Azure にサインインする](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *接続エラーが発生した場合は、 「[トラブルシューティング](#troubleshooting)」を参照してください。*

5. 監視するインストール済みの Web アプリケーションまたは Web サイトを選択し、Application Insights ポータルで結果を表示するときに使用するリソースを構成します。

    ![アプリとリソースを選択します。](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    通常は、新しいリソースと[リソース グループ][roles]を構成します。

    サイトの [Web テスト][availability]や [Web クライアントの監視][client]を既に設定している場合は、既存のリソースを使用します。

6. IIS を再起動します。

    ![ダイアログの上部にある [再起動] を選択します。](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    少しの間、Web サービスが中断されます。

6. 監視対象の Web アプリに ApplicationInsights.config が挿入されます。

    ![Web アプリのコード ファイルの横にある、.config ファイルを見つけます。](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   これ以外にも、web.config にいくつかの変更が加えられます。

#### 後で (再) 構成する

ウィザードを完了した後、いつでも必要に応じてエージェントを再構成できます。この方法は、エージェントをインストールした際の初期設定に問題があった場合にも使用できます。

![タスク バーの Application Insights のアイコンをクリックする](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### アプリが Azure Web アプリとして実行されている場合

Azure の Web アプリのコントロール パネルで、Application Insights 拡張機能を追加します。

![Web アプリで、[設定]、[拡張機能]、[追加]、[Application Insights] の順に選択する](./media/app-insights-asp-net-dependencies/05-extend.png)


### Azure Cloud Services プロジェクトの場合

[スクリプトを Web ロールと worker ロールに追加](app-insights-cloudservices.md#dependencies)します。あるいは、[.NET Framework 4.6 以降](../cloud-services/cloud-services-dotnet-install-dotnet.md)をインストールします。

## <a name="diagnosis"></a>依存関係のパフォーマンスの問題の診断

サーバーでの要求のパフォーマンスを評価するには、[パフォーマンス] ブレードを開き、下方向へスクロールして、要求のグリッドを確認します。

![平均およびカウントを持つ要求の一覧](./media/app-insights-asp-net-dependencies/02-reqs.png)

上部にあるものは、非常に長い時間がかかります。どこに時間がかかるか見つけられるか見てみましょう。

個々の要求イベントを表示するには、その行をクリックします。


![要求回数の一覧](./media/app-insights-asp-net-dependencies/03-instances.png)

実行時間の長いインスタンスがあればそれをクリックし、さらに検査します。

この要求に関連したリモート依存関係呼び出しまで下にスクロールします。

![リモートの依存関係への呼び出しを見つけ、異常な期間を特定します](./media/app-insights-asp-net-dependencies/04-dependencies.png)

この要求に使われた時間のほとんどが、ローカル サービスへの呼び出しに費やされたように見えます。


さらに情報を得るには、その行をクリックします。

![そのリモートの依存関係をクリックし、問題の原因を特定します](./media/app-insights-asp-net-dependencies/05-detail.png)

詳細には、問題の診断に必要な情報が含まれています。


別のケースでは、呼び出しが長い依存関係はありませんが、タイムライン ビューに切り替えることで、内部処理に遅延が発生した箇所を確認できます。


![リモートの依存関係への呼び出しを見つけ、異常な期間を特定します](./media/app-insights-asp-net-dependencies/04-1.png)


## エラー

失敗した要求がある場合は、グラフをクリックします。

![失敗した要求のグラフをクリックします。](./media/app-insights-asp-net-dependencies/06-fail.png)

要求の種類と要求のインスタンスをクリックし、リモートの依存関係への失敗した呼び出しを見つけます。


![要求の種類をクリックし、インスタンスをクリックして同じインスタンスの異なるビューを取得し、それをクリックして例外の詳細を取得します。](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## カスタム依存関係の追跡

標準の依存関係追跡モジュールは、外部の依存関係 (データベース、REST API など) を自動的に検出しますが、同じように扱える追加のコンポーネントが必要になる可能性もあります。

標準のモジュールで使用するのと同じ [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) を使用して、依存関係情報を送信するコードを記述できます。

たとえば、自分で記述していないアセンブリを使ってコードを作成する場合、それに対するすべての呼び出しを測定し、何が応答時間に貢献するかを知ることができます。このデータを Application Insights 内の依存関係グラフに表示するには、データを `TrackDependency` を使用して送信します。

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

標準の依存関係追跡モジュールを無効にするには、[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) の DependencyTrackingTelemetryModule への参照を削除します。

## トラブルシューティング

*依存関係の成功を示すフラグは、常に true と false のいずれかを示します。*

* 最新バージョンの SDK にアップグレードしてください。.NET のバージョンが 4.6 より前の場合は、[Status Monitor](app-insights-monitor-performance-live-website-now.md) をインストールしてください。

## 次のステップ

- [Exceptions](app-insights-asp-net-exceptions.md)
- [ユーザーとページのデータ][client]
- [可用性](app-insights-monitor-web-app-availability.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=AcomDC_0713_2016-->