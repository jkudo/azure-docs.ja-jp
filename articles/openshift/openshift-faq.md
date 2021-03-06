---
title: Azure Red Hat OpenShift に関してよく寄せられる質問
description: Microsoft Azure Red Hat OpenShift に関してよく寄せられる質問への回答を示します
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5901be713f686f0c7213449a775c86b9e346fe12
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275404"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift に関する FAQ

この記事では、Microsoft Azure Red Hat OpenShift に関してよく寄せられる質問 (FAQ) を説明します。

## <a name="which-azure-regions-are-supported"></a>どの Azure リージョンがサポートされていますか?

Azure Red Hat OpenShift がサポートされている世界中のリージョンの一覧については、[サポートされているリソース](supported-resources.md#azure-regions)に関するページを参照してください。

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>既存の仮想ネットワークにクラスターをデプロイできますか?

いいえ。 もっとも、ピアリングを使って Azure Red Hat OpenShift クラスターを既存の VNET に接続することはできます。 詳細については、[クラスターの仮想ネットワークを既存の仮想ネットワークに接続する方法](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)に関するセクションを参照してください。

## <a name="what-cluster-operations-are-available"></a>どのクラスター操作を使用できますか?

コンピューティング ノード数の増大または減少のみを行えます。 作成後は、`Microsoft.ContainerService/openShiftManagedClusters` リソースに対するその他の変更は許可されません。 コンピューティング ノードの最大数は 20 に制限されています。

## <a name="what-virtual-machine-sizes-can-i-use"></a>どの仮想マシンのサイズを使用できますか?

Azure Red Hat OpenShift クラスターで使用できる仮想マシンのサイズの一覧については、[Azure Red Hat OpenShift 仮想マシンのサイズ](supported-resources.md#virtual-machine-sizes)に関するページを参照してください。

## <a name="is-data-on-my-cluster-encrypted"></a>クラスター上のデータは暗号化されていますか?

既定では、保存時の暗号化があります。 Azure Storage プラットフォームは、永続化する前に自動的にデータを暗号化し、取得前にデータの暗号化を解除します。 詳細については、[保存データの Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) に関するページを参照してください。

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Prometheus/Grafana を使用して自分のアプリケーションを監視できますか?

はい、Prometheus を自分の名前空間にデプロイし、自分の名前空間内のアプリケーションを開始できます。

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Prometheus/Grafana を使用して、クラスターの正常性と容量に関連するメトリックを監視できますか?

いいえ、現時点ではできません。

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Jenkins などのツールを使用するできるように、Docker レジストリを外部で使用できますか?

Docker レジストリは `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` から利用できますが、強力なストレージ耐久性保証は得られません。 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) を使用することもできます。

## <a name="is-cross-namespace-networking-supported"></a>名前空間間のネットワークはサポートされていますか?

顧客と個々 のプロジェクト管理者は、`NetworkPolicy` オブジェクトを使用して、プロジェクトごとに名前空間間のネットワークをカスタマイズできます (拒否を含む)。

## <a name="can-an-admin-manage-users-and-quotas"></a>管理者はユーザーおよびクォータを管理できますか?

はい。 Azure Red Hat OpenShift 管理者は、ユーザーおよびクォータを管理するほか、ユーザーが作成したすべてのプロジェクトにアクセスできます。

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>クラスターを特定の Azure AD ユーザーに制限できますか?

はい。 Azure AD アプリケーションを構成することで、クラスターにサインインできる Azure AD ユーザーを制限できます。 詳細については、[ご利用のアプリを特定のユーザー セットに制限する](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)」を参照してください。

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>クラスターは複数の Azure リージョンにわたってコンピューティング ノードを持つことができますか?

いいえ。 Azure Red Hat OpenShift クラスターのすべてのノードは、同じ Azure リージョンに基づく必要があります。

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>マスターおよびインフラストラクチャ ノードは、Azure Kubernetes Service (AKS) の場合と同様に無視されますか?

いいえ。 クラスター マスターを含めすべてのリソースは、顧客サブスクリプションで実行します。 これらの種類のリソースは、読み取り専用のリソース グループに置かれます。

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Open Service Broker for Azure (OSBA) はサポートされていますか?

はい。 OSBA は Azure Red Hat OpenShift と共に使用できます。 詳細については、「[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template)」を参照してください。

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>別のサブスクリプションの仮想ネットワークを詳しく見ようとしていますが、`Failed to get vnet CIDR` エラーを取得します。

仮想ネットワークを含むサブスクリプションでは、必ず `az provider register -n Microsoft.ContainerService --wait` を使用して `Microsoft.ContainerService` プロバイダーを登録してください。 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Azure Red Hat OpenShift (ARO) のメンテナンス プロセスとは何ですか?

ARO 用のメンテナンスには、次の 3 種類があります。アップグレード、etcd データのバックアップと復元、クラウド プロバイダーによって開始されるメンテナンスです。

+ アップグレードには、ソフトウェアのアップグレードと CVE が含まれます。 CVE 修復は、`yum update` を実行することで起動時に発生し、即時のリスク軽減のために提供されます。  並列では、将来のクラスターの作成のために、新しいイメージ ビルドが作成されます。

+ etcd データのバックアップと管理は、アクションに応じて、クラスターのダウンタイムを必要とする可能性がある自動化されたプロセスです。 etcd データベースがバックアップから復元されている場合は、ダウンタイムが発生します。 etcd を 1 時間ごとにバックアップし、過去 6 時間のバックアップを保持します。

+ クラウド プロバイダーによって開始されるメンテナンスには、ネットワーク、ストレージ、リージョンの障害が含まれます。 メンテナンスはクラウド プロバイダーに依存し、プロバイダーによって提供される更新プログラムに依存します。

## <a name="what-is-the-general-upgrade-process"></a>一般的なアップグレード プロセスとは何ですか?

アップグレードの実行は、安全なプロセス実行である必要があり、クラスター サービスを中断しないようにしてください。 SRE では、新しいバージョンが利用可能か、または CVE が未完了である場合に、アップグレード プロセスをトリガーできます。

利用可能な更新プログラムは、ステージング環境でテストされ、運用クラスターに適用されます。 適用すると、新しいノードが一時的に追加され、ノードがローテーション方式で更新されるため、ポッドでレプリカの数を保持できます。 ベスト プラクティスに従うと、ダウンタイムが最小限からなしになるように確保することができます。

保留中のアップグレードまたは更新の重要度によっては、CVE に対するサービスのエクスポージャが軽減されるように、更新プログラムをすぐに適用できるという点で、プロセスが異なる場合があります。 新しいイメージは、クラスターのアップグレードとして、非同期でビルド、テスト、ロール アウトされます。 それ以外の場合は、緊急時と計画メンテナンスの間に違いはありません。 計画メンテナンスは、顧客と共に事前にスケジュールされていません。

顧客との通信が必要な場合は、ICM と電子メールによって通知を送信できます。

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>非常時と計画メンテナンス期間について

Microsoft では、この 2 種類のメンテナンスを区別しません。 Microsoft チームは 24 時間年中無休で利用でき、従来のスケジュールされた "時間外" のメンテナンス期間を使用しません。

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>ホスト オペレーティング システムと OpenShift ソフトウェアはどのように更新されますか?

ホスト オペレーティング システムと OpenShift ソフトウェアは、一般的なアップグレードとイメージのビルド プロセスによって更新されます。

## <a name="whats-the-process-to-reboot-the-updated-node"></a>更新されたノードを再起動するプロセスとは何ですか?

これは、アップグレードの一環として処理される必要があります。

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>ARO では etcd に格納されるデータは暗号化されますか?

etcd レベルでは暗号化されません。 このオプションをオンにすることは、現在サポートされていません。 OpenShift ではこの機能がサポートされますが、これをロードマップに加えるには、エンジニアリングの作業が必要です。 データはディスク レベルで暗号化されます。 詳細については、「[データストア層でのデータの暗号化](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)」を参照してください。

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>基になる VM のログを顧客のログ分析システムにストリーミングできますか?

Syslog、Docker のログ、ジャーナル、dmesg は、マネージド サービスによって処理され、顧客には公開されません。

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>顧客はどのようにしてノード レベルで CPU やメモリなどのメトリックにアクセスし、スケーリングや問題のデバッグなどを実行できますか。ARO クラスターでは `kubectl top` を実行できないようです。

顧客管理者クラスター ロールで `oc adm top nodes` または `kubectl top nodes` コマンドを使用することで、ノード レベルで CPU/メモリ メトリックにアクセスできます。  また、`oc adm top pods` または `kubectl top pods` コマンドを使用して、`pods` の CPU/メモリ メトリックにアクセスすることもできます。

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO の既定のポッド スケジューラ構成とは何ですか?

ARO では、OpenShift に付属する既定のスケジューラを使用します。 ARO でサポートされていない追加のメカニズムがいくつかあります。 詳細については、[既定のスケジューラのドキュメント](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler)と[マスター スケジューラのドキュメント](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)を参照してください。

高度なスケジュールまたはカスタム スケジュールは、現在サポートされていません。 詳細については、[スケジュールのドキュメント](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)を参照してください。

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>デプロイをスケールアップする場合、サービスのすべてのポッドが 1 つの障害ドメインの障害によってダウンしないことを確実にするために、Azure 障害ドメインはポッド配置にどのようにマップされますか?

Azure で仮想マシン スケール セットを使用すると、既定で 5 つの障害ドメインがあります。 スケール セット内の各仮想マシン インスタンスは、これらの障害ドメインのいずれかに配置されます。 これにより、クラスター内のコンピューティング ノードにデプロイされたアプリケーションが別々の障害ドメインに確実に配置されるようになります。

詳細については、「[仮想マシン スケール セットに対する障害ドメインの適切な数を選択する](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)」を参照してください。

## <a name="is-there-a-way-to-manage-pod-placement"></a>ポッドの配置を管理する方法はありますか?

顧客は、顧客管理者としてノードを取得したり、ラベルを表示したりできます。これにより、スケール セット内の任意の VM を対象にする方法が提供されます。

特定のラベルを使用する場合は、次の点に注意してください。

- ホスト名を使うことはできません。 ホスト名はアップグレードと更新によって頻繁にローテーションされるため、変更が保証されます。

- 顧客に特定のラベルまたは配置戦略の要求がある場合は、これを実現できますが、エンジニアリングの作業が必要であり、現時点ではサポートされていません。

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO クラスター内のポッドの最大数とは何ですか?  ARO のノードごとの最大ポッド数とは何ですか?

 Azure Red Hat OpenShift 3.11 ではノードあたりのポッド数が 50 個に制限されており、[ARO ではコンピューティング ノードが 20 個に制限されている](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)ため、ARO クラスターでサポートされるポッドの最大数は 50*20 = 1000 になります。

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>プライベート VNET にデプロイする IP 範囲を指定して、ピアリングされた他の企業 VNET との競合を回避することはできますか?

Azure Red Hat OpenShift では、VNET ピアリングをサポートしており、顧客がピアリングする VNET と、OpenShift ネットワークが動作する VNET CIDR を提供することを許可します。

ARO によって作成された VNET は保護され、構成の変更は受け入れられません。 ピアリングされた VNET は顧客によって制御され、そのサブスクリプションに存在します。

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>クラスターは顧客のサブスクリプション内に存在しますか? 

Azure マネージド アプリケーションは、顧客のサブスクリプションを利用するロックされたリソース グループ内に存在します。 顧客は、その RG 内のオブジェクトを表示できますが、変更はできません。

## <a name="is-the-sdn-module-configurable"></a>SDN モジュールは構成可能ですか?

SDN は openshift-ovs-networkpolicy であり、構成することはできません。

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>マスター/インフラストラクチャ/アプリ ノードでは、どの UNIX 権限 (IaaS 内) を使用できますか?

このオファリングには該当しません。 ノード アクセスは禁止されています。

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>私たちには、どの OCP 権限がありますか? クラスター管理者ですか? プロジェクト管理者ですか?

詳細については、Azure Red Hat OpenShift [クラスター管理者の概要](https://docs.openshift.com/aro/admin_guide/index.html)に関するページを参照してください。

## <a name="which-kind-of-federation-with-ldap"></a>LDAP と共に利用されるのは、どの種類のフェデレーションですか?

これは、Azure AD の統合によって実現されます。 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>他の顧客と共有される ARO 内の要素はありますか? それとも、すべてが独立しているのでしょうか?

各 Azure Red Hat OpenShift クラスターは指定された顧客専用であり、その顧客のサブスクリプション内に存在します。 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>任意の永続ストレージ ソリューション (OCSなど) を選ぶことはできますか。 

2 つのストレージ クラスから選ぶことができます。Azure Disk と Azure File です。

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>クラスターはどのように更新されますか (脆弱性に起因するメジャーとマイナーを含む)。

「[一般的なアップグレード プロセスとは何ですか?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)」を参照してください。

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>どの Azure Load Balancer が ARO によって使用されますか?  Standard ですか、それとも Basic ですか、また、構成は可能ですか?

ARO では Standard の Azure Load Balancer が使用され、構成することはできません。

## <a name="can-aro-use-netapp-based-storage"></a>ARO では、NetApp ベースのストレージを使用できますか?

現時点では、サポートされているストレージ オプションは、Azure Disk および Azure File ストレージ クラスだけです。 


