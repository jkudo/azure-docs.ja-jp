---
title: MARS エージェントを使用して Windows マシンをバックアップする
description: Azure Backup Microsoft Recovery Services (MARS) エージェントを使用して、Windows マシンをバックアップします。
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025539"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Azure Backup MARS エージェントを使用して Windows マシンをバックアップする

この記事では、[Azure Backup](backup-overview.md) サービスと、Azure Backup エージェントとも呼ばれる Microsoft Azure Recovery Services (MARS) エージェントを使用して、Windows マシンをバックアップする方法について説明します。

この記事では、次の方法について説明します。

> [!div class="checklist"]
>
> * 前提条件を確認し、Recovery Services コンテナーを作成する。
> * MARS エージェントをダウンロードし、設定する
> * バックアップ ポリシーとスケジュールを作成する。
> * オンデマンド バックアップを実行する。

## <a name="about-the-mars-agent"></a>MARS エージェントについて

MARS エージェントは、オンプレミスのマシンや Azure VM から Azure 上の Recovery Services バックアップ コンテナーにファイル、フォルダー、システム状態をバックアップするために Azure Backup によって使用されます。 エージェントは次のように実行できます。

* Azure 上の Recovery Services バックアップ コンテナーに直接バックアップできるように、オンプレミスの Windows マシン上でエージェントを直接実行します。
* VM 上の特定のファイルとフォルダーをバックアップするには、(Azure VM バックアップ拡張機能を使用してサイドバイサイドで) Windows を実行している Azure VM 上でエージェントを実行します。
* Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) サーバー上でエージェントを実行します。 このシナリオでは、マシンとワークロードを MABS/DPM にバックアップした後、MARS エージェントを使用して MABS/DPM が Azure 上のコンテナーにバックアップされます。
バックアップできる内容は、エージェントがインストールされている場所によって異なります。

> [!NOTE]
> Azure VM のバックアップの主な方法は、VM 上で Azure Backup 拡張機能を使用することです。 これにより、VM 全体がバックアップされます。 VM 上の特定のファイルとフォルダーをバックアップする場合は、拡張機能と共に、MARS エージェントをインストールして使用できます。 [詳細については、こちらを参照してください](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![バックアップ プロセスの手順](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>開始する前に

* Azure Backup が MARS エージェントを使用して Windows マシンをバックアップする[方法を確認](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)します。
* セカンダリ MABS または DPM サーバー上で MARS エージェントを実行しているバックアップ アーキテクチャについて[確認](backup-architecture.md#architecture-back-up-to-dpmmabs)します。
* サポートされている内容と MARS エージェントを使用してバックアップできるものを[確認](backup-support-matrix-mars-agent.md)します。
* バックアップするマシン上でインターネット アクセスを確認します。
* サーバー (またはクライアント) を Azure にバックアップするには、Azure アカウントが必要です。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成できます。

### <a name="verify-internet-access"></a>インターネット アクセスを確認する

マシンのインターネットへのアクセスが制限されている場合は、マシンまたはプロキシのファイアウォール設定によって次の URL と IP アドレスが許可されることを確認します。

#### <a name="urls"></a>URL

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP アドレス

* 20.190.128.0/18
* 40.126.0.0/18

上記のすべての URL と IP アドレスにアクセスするには、ポート 443 で HTTPS プロトコルを使用します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーには、経時的に作成されたすべてのバックアップと復旧ポイントが格納され、バックアップ マシンに適用されるバックアップ ポリシーが含まれます。 次の手順に従ってコンテナーを作成します。

1. Azure サブスクリプションを使用して、[Azure Portal](https://portal.azure.com/) にサインインします。

2. **[Recovery Services コンテナー]** を検索して選択します。

    ![Create Recovery Services Vault step 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. **[Recovery Services コンテナー]** メニューの **[追加]** をクリックします。

    ![Create Recovery Services Vault step 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

   * 名前は Azure サブスクリプションに対して一意である必要があります。
   * 2 から 50 文字を含めることができます。
   * 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。

5. Azure サブスクリプションとリソース グループ、およびコンテナーを作成する必要のある geography 型のリージョンを選択します。 バックアップ データがコンテナーに送信されます。 **[Create]** をクリックします。

    ![Recovery Services コンテナーの作成手順 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * コンテナーが作成されるまで時間がかかることがあります。
   * ポータルの右上の領域で、状態の通知を監視します。 数分経過してもコンテナーが表示されない場合は、 **[最新の情報に更新]** をクリックしてください。

     ![[最新の情報に更新] ボタンをクリックする](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>ストレージ冗長性の設定

Azure Backup では、コンテナーのストレージが自動的に処理されます。 ストレージをレプリケートする方法を指定する必要があります。

1. **[Recovery Services コンテナー]** ブレードで、新しいコンテナーをクリックします。 **[設定]** セクションの **[プロパティ]** をクリックします。
2. **[プロパティ]** で、 **[バックアップ構成]** の **[更新]** をクリックします。

3. ストレージのレプリケーションの種類を選択し、 **[保存]** をクリックします。

      ![新しいコンテナーのストレージ構成を設定する](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き既定の **geo 冗長**設定を使用することをお勧めします。
* プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、 **[ローカル冗長]** を選択します。これにより、Azure Storage のコストを削減できます。
* [geo](../storage/common/storage-redundancy-grs.md) 冗長と[ローカル](../storage/common/storage-redundancy-lrs.md)冗長の詳細をご確認ください。

## <a name="download-the-mars-agent"></a>MARS エージェントのダウンロード

バックアップ対象のマシンにインストールする MARS エージェントをダウンロードします。

* いずれかのマシンにエージェントを既にインストールした場合は、最新バージョンを実行していることを確認します。
* 最新バージョンは、ポータルで、または[直接ダウンロード](https://aka.ms/azurebackup_agent)を使用して入手できます

1. コンテナーの **[作業の開始]** で、 **[バックアップ]** をクリックします。

    ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. **[ワークロードはどこで実行されていますか?]** で **[オンプレミス]** を選択します。 Azure VM 上に MARS エージェントをインストールする場合でも、このオプションを選択する必要があります。
3. **[何をバックアップしますか?]** で、 **[ファイルとフォルダー]** または **[システム状態]** 、あるいはその両方を選択します。 他にも多くのオプションを使用可能ですが、これらはセカンダリ バックアップ サーバーを実行している場合にのみサポートされます。 **[インフラストラクチャの準備]** をクリックします。

      ![ファイルとフォルダーの構成](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. **[インフラストラクチャの準備]** の **[Recovery Services エージェントのインストール]** で、MARS エージェントをダウンロードします。

    ![[Download Agent for Windows Server or Windows Client]](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. ダウンロードのポップアップ メニューで、 **[保存]** をクリックします。 既定では、 **MARSagentinstaller.exe** ファイルがダウンロード フォルダーに保存されます。

6. ここで、 **[Already download or using the latest Recovery Services Agent]\(既にダウンロードしたか、最新の Recovery Services エージェントを使用している\)** をオンにし、コンテナー資格情報をダウンロードします。

    ![コンテナー資格情報のダウンロード](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. **[保存]** をクリックします。 ファイルはダウンロード フォルダーにダウンロードされます。 コンテナー資格情報ファイルを開くことはできません。

## <a name="install-and-register-the-agent"></a>エージェントをインストールして登録する

1. バックアップ対象のマシン上で **MARSagentinstaller.exe** ファイルを実行します。
2. MARS Agent Setup (MARS エージェントのセットアップ) ウィザードの **[インストールの設定]** で、エージェントをインストールする場所と、キャッシュに使用する場所を指定します。 続けて、 **[次へ]** をクリックします。
   * Azure Backup では、キャッシュを使用して、Azure に送信する前のデータのスナップショットを格納します。
   * キャッシュの場所には、バックアップするデータのサイズの 5% 以上に相当する空き容量が必要です。

    ![MARS ウィザードのインストール設定](./media/backup-configure-vault/mars1.png)

3. **[プロキシの構成]** で、Windows マシン上で実行されているエージェントがインターネットに接続する方法を指定します。 続けて、 **[次へ]** をクリックします。

   * カスタム プロキシを使用している場合は、プロキシ設定と、必要に応じて資格情報を指定します。
   * エージェントには[こちらの URL](#verify-internet-access) へのアクセス権が必要であることを忘れないでください。

     ![MARS ウィザードのインターネット アクセス](./media/backup-configure-vault/mars2.png)

4. **[インストール]** で、前提条件チェックをレビューし、 **[インストール]** をクリックします。
5. エージェントがインストールされたら、 **[登録処理を続行]** をクリックします。
6. **[サーバーの登録ウィザード]**  >  **[資格情報コンテナーの識別]** で、ダウンロードした資格情報ファイルを参照し、選択します。 続けて、 **[次へ]** をクリックします。

    ![登録 - コンテナー資格情報](./media/backup-configure-vault/register1.png)

7. **[暗号化の設定]** で、マシンのバックアップの暗号化と復号化に使用されるパスフレーズを指定します。

    * 暗号化パスフレーズを安全な場所に保存します。
    * パスフレーズを紛失または忘れた場合、Microsoft がバックアップ データの回復を支援することはできません。 安全な場所にファイルを保存してください。 バックアップを復元するために必要になります。

8. **[完了]** をクリックします。 エージェントがインストールされ、コンピューターがコンテナーに登録されました。 バックアップを構成してスケジュールする準備ができました。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、復旧ポイントを作成するためにデータのスナップショットを取得するタイミングと、復旧ポイントを保持する期間を指定します。

* MARS エージェントを使用してバックアップ ポリシーを構成します。
* Azure Backup では、夏時間 (DST) は自動的には考慮されません。 このため、実際の時刻とスケジュールされたバックアップ時刻の間で矛盾が生じる可能性があります。

次のようにポリシーを作成します。
1. MARS エージェントをダウンロードして登録した後で、エージェント コンソールを起動します。 エージェントは、コンピューターで **Microsoft Azure Backup**を検索すると見つかります。  
2. **[アクション]** で、 **[バックアップのスケジュール]** をクリックします。

    ![Schedule a Windows Server backup](./media/backup-configure-vault/schedule-first-backup.png)
3. バックアップのスケジュール ウィザードの **[作業の開始]** ページで、 **[次へ]** をクリックします。
4. **[バックアップする項目の選択]** で、 **[項目の追加]** をクリックします。

    ![バックアップする項目の選択](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. **[項目の選択]** で、バックアップする対象を選択し、 **[OK]** をクリックします。

    ![バックアップするよう選択した項目](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. **[バックアップする項目の選択]** ページで、 **[次へ]** をクリックします。
7. **[バックアップ スケジュールの選択]** ページで、毎日または毎週のバックアップをいつ実行するかを指定します。 続けて、 **[次へ]** をクリックします。

    - バックアップの作成時に復旧ポイントが作成されます。
    - 環境内で作成される復旧ポイントの数は、バックアップ スケジュールによって異なります。


8. 毎日のバックアップは 1 日に 3 回までスケジュールすることができます。 たとえば、スクリーンショットには、午前 0 時と午後 6 時の 2 つの毎日のバックアップが表示されています。

    ![毎日のスケジュール](./media/backup-configure-vault/day-schedule.png)


9. 毎週のバックアップを実行することもできます。 たとえばこのスクリーンショットは、1 週間おきの日曜日と水曜日の午前 9 時 30 分と午前 1 時に作成されるバックアップが示されています。

    ![週単位のスケジュール](./media/backup-configure-vault/week-schedule.png)


10. **[保持ポリシーの選択]** ページで、データの履歴コピーを格納する方法を指定します。 続けて、 **[次へ]** をクリックします。

    - 保持設定では、格納する必要のある復旧ポイントと、それらを格納する期間を指定します。
    - たとえば、日単位の保持設定を設定する場合は、日単位の保持に対して指定した時刻に、最新の復旧ポイントが指定した日数にわたって保持されることを指定します。 または、別の例として、月単位の保持ポリシーを指定して、毎月 30 日に作成された復旧ポイントを 12 か月間保存する必要があることを指定できます。
    - 毎日および毎週の復旧ポイントの保持は、通常はバックアップ スケジュールと一致します。 つまり、スケジュールに従ってバックアップがトリガーされると、バックアップによって作成された復旧ポイントが、日単位または週単位の保持ポリシーで指定された期間保存されます。
    - たとえば、次のスクリーンショットでは:

        -   午前 0 時と午後 6 時の毎日のバックアップは 7 日間保持されます。
        -   土曜日の午前 0 時と午後 6 時に作成されるバックアップは 4 週間保持されます。
        -   月の最後の週の土曜日の午前 0 時と午後 6 時に作成されるバックアップは 12 か月間保持されます。
        -   3 月の最後の週の土曜日に作成されるバックアップは 10 年間保持されます。

        ![保持の例](./media/backup-configure-vault/retention-example.png)


11. **[初期バックアップの種類の選択]** で、初回のバックアップをネットワーク経由で実行するか、オフライン バックアップを使用するかを決定します (オフライン バックアップの詳細については、この[記事](offline-backup-azure-data-box.md)を参照してください)。 ネットワーク経由で初回のバックアップを実行するには、 **[自動でネットワーク経由]** を選択し、 **[次へ]** をクリックします。

    ![初回バックアップの種類](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. **[確認]** で情報を確認し、 **[完了]** をクリックします。

    ![バックアップの種類を確認する](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. ウィザードでバックアップ スケジュールの作成が完了したら、 **[閉じる]** をクリックします。

    ![バックアップ プロセスの変更を確認する](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

エージェントがインストールされる各コンピューターにポリシーを作成する必要があります。

### <a name="perform-the-initial-backup-offline"></a>オフラインでの初期バックアップの実行

初期バックアップは、ネットワーク経由で自動的に実行することも、オフラインで実行することもできます。 初期バックアップのオフライン シード処理は、転送するために大量のネットワーク帯域幅が必要になる大量のデータがある場合に便利です。 オフライン転送は次のように行います。

1. ステージングの場所にバックアップ データを書き込みます。
2. AzureOfflineBackupDiskPrep ツールを使って、ステージングの場所から 1 つまたは複数の SATA ディスクにデータをコピーします。
3. このツールでは、Azure インポート ジョブが作成されます。 Azure のインポートとエクスポートの[詳細](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)をご確認ください。
4. SATA ディスクを Azure データセンターに送信します。
5. Azure データセンターでは、ディスク データが Azure ストレージ アカウントにコピーされます。
6. Azure Backup によってストレージ アカウントからコンテナーにデータがコピーされ、増分バックアップがスケジュールされます。

オフライン シード処理の[詳細](offline-backup-azure-data-box.md)をご確認ください。

### <a name="enable-network-throttling"></a>ネットワーク調整の有効化

ネットワーク調整を有効にして、MARS エージェントによってネットワーク帯域幅が使用される方法を制御できます。 調整は、作業時間中にデータをバックアップする必要があり、バックアップと復元のアクティビティに使用される帯域幅の量を制御したい場合に便利です。

* Azure Backup のネットワーク調整では、ローカル オペレーティング システムの[サービス品質 (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) を利用します。
* バックアップのネットワーク調整は、Windows Server 2012 以降と、Windows 8 以降で利用できます。 オペレーティング システムでは、最新の Service Pack が実行されている必要があります。

次のようにしてネットワーク調整を有効にします。

1. MARS エージェントで、 **[プロパティの変更]** をクリックします。
2. **[調整]** タブで、 **[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** をオンにします。

    ![Network throttling](./media/backup-configure-vault/throttling-dialog.png)
3. 作業中と作業時間外に使用できる帯域幅を指定します。 帯域幅の値は 512 Kbps から開始し、最大は 1,023 MBps です。 次に、 **[OK]** をクリックします

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

1. MARS エージェントで、 **[今すぐバックアップ]** をクリックします。

    ![Windows Server を今すぐバックアップする](./media/backup-configure-vault/backup-now.png)

2. MARS エージェントのバージョンが 2.0.9169.0 以降の場合は、カスタム保有期間を設定できます。 **[バックアップの保持期限]** セクションで、表示されているカレンダーから日付を選択します。

   ![バックアップの保持カレンダー](./media/backup-configure-vault/mars-ondemand.png)

3. **[確認]** で、設定を確認し、 **[バックアップ]** をクリックします。
4. **[閉じる]** をクリックしてウィザードを閉じます。 バックアップが完了する前にこれを行った場合、ウィザードはバックグラウンドで引き続き実行されます。
5. 初回バックアップが完了すると、 **[ジョブは完了しました]** 状態が Backup コンソールに表示されます。

## <a name="on-demand-backup-policy-retention-behavior"></a>オンデマンド バックアップ ポリシーの保持動作

>[!NOTE]
>2\.0.9169.0 より前の MARS エージェント バージョンにのみ適用されます
>

* 詳細については、「[バックアップ ポリシーの作成](backup-configure-vault.md#create-a-backup-policy)」の手順 8 を参照してください。

| バックアップ スケジュール オプション | バックアップ データが保持される期間
| -- | --
| バックアップ スケジュールの指定頻度: *日 | **既定の保持期間**: "毎日のバックアップの保持日数" に相当します。 <br/><br/> **例外**: 長期保有 (週、月、年) の対象としてスケジュールされた毎日のバックアップ セットが失敗した場合、このスケジュールされたバックアップが失敗した直後に発生したオンデマンド バックアップが、長期保有の対象として考慮されます。 それ以外の場合、スケジュールされた次回バックアップが長期保有の対象として考慮されます。<br/><br/> **例**:(たとえば) 木曜日午前 8 時のスケジュールされたバックアップの作成が失敗し、その同じバックアップが週単位、月単位、年単位の保持の対象として考慮されている場合、スケジュールされた次回バックアップ (たとえば、金曜日午前 8 時) より前にトリガーされた最初のオンデマンド バックアップが、木曜日午前 8 時のバックアップに対して、週単位、月単位、年単位の保持の対象として自動的に適宜タグ付けされます。
| バックアップ スケジュールの指定頻度: *週単位 | **既定の保持期間**: 1 日 <br/> 週単位のバックアップ ポリシーを使用しているデータ ソースに対して作成されたオンデマンド バックアップは、データ ソースの最新のバックアップであっても、次の日に削除されます。 <br/><br/> **例外**: 長期保有 (週、月、年) の対象としてスケジュールされた週単位のバックアップ セットが失敗した場合、このスケジュールされたバックアップが失敗した直後に発生したオンデマンド バックアップが、長期保有の対象として考慮されます。 それ以外の場合、スケジュールされた次回バックアップが長期保有の対象として考慮されます。 <br/><br/> **例**:(たとえば) 木曜日午前 8 時のスケジュールされたバックアップの作成が失敗し、その同じバックアップが月単位または年単位の保持の対象として考慮されている場合、スケジュールされた次回バックアップ (たとえば、木曜日午前 8 時) より前にトリガーされた最初のオンデマンド バックアップが、木曜日午前 8 時のバックアップに対して、月単位または年単位の保持の対象として自動的に適宜タグ付けされます。

## <a name="next-steps"></a>次のステップ

[ファイルを復元する方法を確認します](backup-azure-restore-windows-server.md)。
