

イメージは、Azure でオペレーティング システムを持つ新しい仮想マシンを提供するために使用します。また、イメージには 1 つ以上のデータ ディスクも保持できます。イメージは複数のソースから利用できます。

-	Azure は、[Marketplace](https://azure.microsoft.com/gallery/virtual-machines/) でイメージを提供しています。最近のバージョンの Windows Server や Linux オペレーティング システムのディストリビューションがあります。また、いくつかのイメージには、SQL Server などのアプリが含まれます。MSDN 特典や MSDN の従量課金制サブスクライバーでは、その他のイメージにアクセスできます。
-	オープン ソース コミュニティは、[VM Depot](http://vmdepot.msopentech.com/List/Index) でイメージを提供しています。
-	また、既存の Azure 仮想マシンをキャプチャしてイメージとして使用するか、イメージをアップロードすることで、Azure 内で独自のイメージを使用することもできます。

## VM イメージと OS イメージについて

Azure では、*VM イメージ* と *OS イメージ*の 2 つの種類のイメージを使用できます。VM イメージには、オペレーティング システムと、イメージの作成時に、仮想マシンにアタッチされているすべてのディスクが含まれています。これは新しいタイプのイメージです。VM イメージが導入される以前は、Azure 内のイメージが保持できるのは汎用オペレーティング システムのみで、追加のディスクは保持できませんでした。汎用オペレーティング システムのみを含む VM イメージは、基本的に元のタイプのイメージと同じで、これを OS イメージといいます。

Azure 内の仮想マシンや、他の場所で稼働している仮想マシンを元にして、コピーおよびアップロードして独自のイメージを作成できます。イメージを使用して、1 つ以上の仮想マシンを作成する場合は、準備としてイメージを一般化する必要があります。Windows Server イメージを作成するには、.vhd ファイルをアップロードする前に、サーバー上で Sysprep コマンドを実行して一般化を行います。Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://go.microsoft.com/fwlink/p/?LinkId=392030)」を参照してください。Linux イメージを作成するには、Azure Linux エージェントを実行する他に、ソフトウェアのディストリビューションにより、固有のコマンドを実行する必要があります。

<!---HONumber=AcomDC_0727_2016-->