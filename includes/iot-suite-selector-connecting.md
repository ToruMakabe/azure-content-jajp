> [AZURE.SELECTOR]
- [Windows 上の C](../articles/iot-suite/iot-suite-connecting-devices.md)
- [Linux 上の C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [mbed 上の C](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.JS](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## シナリオの概要

このシナリオでは、次のテレメトリをリモート監視[構成済みソリューション][lnk-what-are-preconfig-solutions]に送信するデバイスを作成します。

- 外部温度
- 内部温度
- 湿度

わかりやすくするために、デバイス上のコードではサンプル値を生成しますが、デバイスに実際のセンサーを接続し、実際のテレメトリを送信して、サンプルを拡張することをお勧めします。

このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。

## 開始する前に

デバイス用のコードを作成する前に、リモート監視構成済みソリューションをプロビジョニングし、そのソリューションに新しいカスタム デバイスをプロビジョニングする必要があります。

### リモート監視構成済みソリューションをプロビジョニングする

このチュートリアルで作成するデバイスは、[リモート監視][lnk-remote-monitoring]構成済みソリューションのインスタンスにデータを送信します。リモート監視構成済みソリューションを Azure アカウントにまだプロビジョニングしていない場合は、次の手順に従います。

1. <https://www.azureiotsuite.com/> ページで、**[+]** をクリックして新しいソリューションを作成します。

2. **[リモート監視]** パネルで **[選択]** をクリックして、新しいソリューションを作成します。

3. **[「リモートの監視」ソリューションを作成する]** ページで任意の**ソリューション名**を入力し、デプロイ先の**リージョン**を選択して、使用する Azure サブスクリプションを選択します。その後、**[ソリューションの作成]** をクリックします。

4. プロビジョニング プロセスが完了するまで待機します。

> [AZURE.WARNING] 構成済みソリューションでは、課金対象の Azure サービスを使用します。不必要な課金を避けるために、使用が済んだら、必ずサブスクリプションから構成済みソリューションを削除してください。<https://www.azureiotsuite.com/> ページにアクセスして、構成済みソリューションをサブスクリプションから完全に削除することができます。

リモート監視ソリューションのプロビジョニング プロセスが完了したら、**[起動]** をクリックしてブラウザーでソリューション ダッシュボードを開きます。

![][img-dashboard]

### リモート監視ソリューションでデバイスをプロビジョニングする

> [AZURE.NOTE] ソリューションにデバイスを既にプロビジョニングしている場合は、この手順を省略して構いません。クライアント アプリケーションを作成するときに、デバイスの資格情報が必要になります。

デバイスが構成済みソリューションに接続するには、有効な資格情報を使用して IoT Hub に対してデバイス自身の ID を証明する必要があります。デバイスの資格情報は、ソリューション ダッシュボードから取得できます。このチュートリアルの後半で、クライアント アプリケーションにデバイスの資格情報を含めます。

新しいデバイスをリモート監視ソリューションに追加するには、ソリューション ダッシュボードで次の手順を実行します。

1.  ダッシュボードの左下隅にある **[デバイスの追加]** をクリックします。

    ![][1]

2.  **[カスタム デバイス]** パネルで、**[新規追加]** をクリックします。

    ![][2]

3.  **[自分で自分のデバイス ID を定義する]** を選択し、**mydevice** などのデバイス ID を入力します。**[ID の確認]** をクリックして、その名前がまだ使用されていないことを確認し、**[作成]** をクリックしてデバイスをプロビジョニングします。

    ![][3]

5. デバイスの資格情報 (デバイス ID、IoT Hub ホスト名、デバイス キー) をメモしておきます。クライアント アプリケーションがリモート監視ソリューションに接続する際に、この資格情報が必要になります。次に、**[Done]** をクリックします。

    ![][4]

6. デバイス セクションにデバイスが表示されていることを確認します。デバイスがリモート監視ソリューションへの接続を確立するまで、デバイスの状態が **[保留中]** になります。

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0720_2016-->