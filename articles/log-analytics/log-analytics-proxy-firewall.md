<properties
	pageTitle="Log Analytics のプロキシ設定とファイアウォール設定の構成 | Microsoft Azure"
	description="エージェントや OMS サービスで特定のポートを使用する必要がある場合は、プロキシとファイアウォールの設定を構成します。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics のプロキシ設定とファイアウォール設定の構成


Operations Manager やそのエージェントと使用する場合と、サーバーに直接接続する Microsoft Monitoring Agent を使用する場合では、OMS の Log Analytics のプロキシとファイアウォールの設定に必要な操作が異なります。使用するエージェントの種類については、以下のセクションを確認してください。

## Microsoft Monitoring Agent でプロキシとファイアウォールの設定を構成する

OMS サービスに Microsoft Monitoring Agent を接続して登録するには、ドメインのポート番号と URL へのアクセスが必要になります。エージェントと OMS サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。インターネットへのアクセスを制限するためにファイアウォールを使用する場合は、OMS へのアクセスを許可するようにファイアウォールを構成する必要があります。次の表には、OMS で必要なポートの一覧を示しています。

|**エージェントのリソース**|**ポート**|
|--------------|-----|
|*.ods.opinsights.azure.com|ポート 443|
|*.oms.opinsights.azure.com|ポート 443|
|ods.systemcenteradvisor.com|ポート 443|
|*.blob.core.windows.net|Port 443|

コントロール パネルから Microsoft Monitoring Agent のプロキシ設定を構成する際には、以下の手順を使用してください。この手順は、各サーバーに対して行う必要があります。構成が必要なサーバーの数が多い場合には、このプロセスを自動化するスクリプトを使った方が作業が簡単に済むことも考えられます。そのような場合は、1 つ先の手順の「[スクリプトを使って Microsoft Monitoring Agent のプロキシ設定を構成するには](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script)」をご覧ください。

### コントロール パネルを使って Microsoft Monitoring Agent のプロキシ設定を構成するには

1. **[コントロール パネル]** を開きます。

2. **[Microsoft Monitoring Agent]** を開きます。

3. **[プロキシ設定]** タブをクリックします。
  ![proxy settings tab](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)

4. 例に示したように、**[プロキシ サーバーを使用する]** をオンにして、URL と (必要に応じて) ポート番号を入力します。プロキシ サーバーで認証が必要な場合には、プロキシ サーバーにアクセスするためのユーザー名とパスワードを入力します。

次の手順に従って PowerShell スクリプトを作成し、プロキシ設定を設定して各エージェントがサーバーに直接接続できるようにします。

### スクリプトを使って Microsoft Monitoring Agent のプロキシ設定を構成するには


- 次の例をコピーして、使用する環境にあった情報を使って更新します。PS1 ファイル名の拡張子で保存して、OMS サービスに直接接続する各コンピューターでスクリプトを実行します。


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## Operations Manager でプロキシとファイアウォールの設定を構成する

OMS サービスに Operations Manager 管理グループを接続し登録するには、ドメインのポート番号と URL へのアクセスが必要になります。Operations Manager 管理サーバーと OMS サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。インターネットへのアクセスを制限するためにファイアウォールを使用する場合は、OMS へのアクセスを許可するようにファイアウォールを構成する必要があります。Operations Manager 管理サーバーがプロキシ サーバーの背後にない場合でも、そのエージェントは存在する可能性があります。この場合、セキュリティとログの管理ソリューションのデータを OMS Web サービスに送信することを許可するには、プロキシ サーバーをエージェントと同じように構成する必要があります。

Operations Manager エージェントが OMS サービスと通信するには、Operations Manager インフラストラクチャ (エージェントを含む) に適切なプロキシ設定とバージョンが必要です。エージェントのプロキシ設定は、Operations Manager コンソールで指定します。バージョンは次のいずれかにする必要があります。

- Operations Manager 2012 SP1 更新プログラム ロールアップ 7 以降
- Operations Manager 2012 R2 更新プログラム ロールアップ 3 以降


次の表では、これらのタスクに関連するポートを一覧を示します。

>[AZURE.NOTE] 次の一部のリソースは、OMS の旧バージョンである Advisor と Operational Insights を指しています。表示されたリソースは、今後変更される予定です。

エージェントのリソースとポートの一覧は次のとおりです。

|**エージェントのリソース**|**ポート**|
|--------------|-----|
|*.ods.opinsights.azure.com|ポート 443|
|*.oms.opinsights.azure.com|ポート 443|
|ods.systemcenteradvisor.com|ポート 443|
|*.blob.core.windows.net/|Port 443|

管理サーバーのリソースとポートの一覧は次のとおりです。

|**管理サーバーのリソース**|**ポート**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443|
|service.systemcenteradvisor.com|Port 443|
|scadvisor.accesscontrol.windows.net|Port 443|
|scadvisorservice.accesscontrol.windows.net|Port 443|
|*.blob.core.windows.net|ポート 443|
|data.systemcenteradvisor.com|ポート 443|
|ods.systemcenteradvisor.com|ポート 443|
|*.systemcenteradvisor.com|ポート 443|

OMS と Operations Manager コンソールのリソースとポートの一覧は次のとおりです。

|**OMS と Operations Manager コンソールのリソース**|**ポート**|
|----|----|
|*.systemcenteradvisor.com|ポート 80 および 443|
|*.live.com|ポート 80 と 443|
|*.microsoftonline.com|ポート 80 および 443|
|login.windows.net|ポート 80 および 443|



次の手順に従って Operations Manager 管理グループを OMS サービスに登録します。管理グループと OMS サービス間で通信の問題が発生する場合、検証手順を使用して OMS サービスへのデータ送信のトラブルシューティングを行います。

### OMS サービス エンドポイントの例外を要求するには

1. 前述の最初のテーブルからの情報を使用して、Operations Manager 管理サーバーに必要なリソースが、ファイアウォール経由でアクセスできることを確認します。
2. 前述の最初のテーブルからの情報を使用して、Operations Manager の Operations コンソールと OMS に必要なリソースが、ファイアウォール経由でアクセスできることを確認します。
3. Internet Explorer でプロキシ サーバーを使用する場合は、正しく構成され、機能していることを確認します。確認するには、セキュリティで保護された Web 接続 (https) を開きます ([https://bing.com](https://bing.com) など)。セキュリティで保護された Web 接続がブラウザーで動作しない場合は、クラウドに Web サービスがある Operations Manager 管理コンソールでもおそらく作動しません。

### Operations Manager コンソールでプロキシ サーバーを構成するには

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。

2. **[Operational Insights]** を展開して、**[Operational Insights の接続]** をクリックします。
    ![Operations Manager OMS の接続](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. [OMS の接続] ビューで、**[プロキシ サーバーの構成]** をクリックします。
    ![Operations Manager OMS の接続、プロキシ サーバーの構成](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. [Operational Insights 設定ウィザード: プロキシ サーバー] で **[Operational Insights Web サービスへのアクセスにプロキシ サーバーを使用する]** を選択して、ポート番号と URL を入力します (例: **http://myproxy:80**.  
    ![Operations Manager OMS のプロキシ アドレス](./media/log-analytics-proxy-firewall/proxy-om03.png))。


### プロキシ サーバーで認証が必要な場合の資格情報を指定するには
 プロキシ サーバーの資格情報と設定は、OMS にデータを送信する管理対象コンピューターに反映する必要があります。これらのサーバーは、*Microsoft System Center Advisor Monitoring Server Group* にある必要があります。資格情報は、グループ内の各サーバーのレジストリに暗号化されます。

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。
2. **[RunAs Configuration (RunAs の構成)]** で **[Profiles (プロファイル)]** を選択します。
3. **System Center Advisor Run As Profile Proxy** というプロファイルを開きます。
    ![System Center Advisor Run As Proxy profile のイメージ](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. Run As Profile (Run As プロファイル) ウィザードで **[追加]** をクリックして Run As アカウント (実行アカウント) を使用します。新しい Run As アカウント(実行アカウント) を作成するか、既存のアカウントを使用できます。このアカウントには、プロキシ サーバーを通過するための十分な権限を持たせる必要があります。  
![Run As Profile Wizard のイメージ](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. 管理するアカウントを設定するには、**[選択したクラス、グループ、またはオブジェクト]** をクリックして、[オブジェクトの検索] ボックスを開きます。  
    ![Run As Profile Wizard のイメージ](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. **Microsoft System Center Advisor Monitoring Server Group** を検索して選択します。
    ![Object Search boxc のイメージ](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. **[OK]** をクリックして、[実行アカウントの追加] ボックスを閉じます。  
![Run As Profile Wizard のイメージ](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. ウィザードを完了し、変更を保存します。  
    ![Run As Profile Wizard のイメージ](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)


### OMS の管理パックがダウンロードされることを確認するには

- OMS にソリューションを追加した場合、ソリューションは Operations Manager コンソールの **[Administration]** (管理) で管理パックとして表示されます。「*System Center Advisor*」と入力して検索すると、早く見つけることができます。  
    ![ダウンロードした管理パック](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png)
- また、Operations Manager 管理サーバーで次の Windows PowerShell コマンドを使用する方法でも、OMS の管理パックを確認できます。

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### Operations Manager が OMS サービスにデータを送信していることを確認するには

1. Operations Manager 管理サーバーで、パフォーマンス モニター (perfmon.exe) を開き、**[Performance Monitor (パフォーマンス モニター)]** を選択します。
2. **[追加]** をクリックして、**[Health Service Management Groups]** を選択します。
3. **HTTP** から始まるすべてのカウンターを追加します。
    ![カウンターの追加](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Operations Manager の構成が適切であれば、OMS と構成済みのログ収集ポリシーで追加した、管理パックに基づく Health Service Management カウンターのイベントやその他のデータ アイテムにおけるアクティビティが表示されます。  
    ![アクティビティが表示されたパフォーマンス モニター](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)


## Azure Automation の Hybrid Runbook Worker

Hybrid Runbook Worker をサポートするための受信ファイアウォールの要件はありません。

Hybrid Runbook Worker を実行するオンプレミスのコンピューターは、ポート 443、9354、および 30000-30199 上の *.cloudapp.net への発信アクセス権を有する必要があります。

## 次のステップ

- 機能を追加し、データを収集する方法については、「[Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md)」 (ソリューションギャラリーから Log Analytics ソリューションを追加する) を参照してください。
- [ログ検索](log-analytics-log-searches.md)について理解を深め、ソリューションによって収集された情報の詳細を確認します。

<!---HONumber=AcomDC_0525_2016-->