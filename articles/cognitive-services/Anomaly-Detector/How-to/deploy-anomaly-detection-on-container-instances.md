---
title: Anomaly Detector コンテナーを Azure Container Instances で実行する
titleSuffix: Azure Cognitive Services
description: Anomaly Detector コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716358"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Anomaly Detector コンテナーを Azure Container Instances にデプロイする

Cognitive Services [Anomaly Detector](../anomaly-detector-container-howto.md) コンテナーを Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) にデプロイする方法について説明します。 この手順では、Anomaly Detector リソースの作成方法を実演します。 次に、関連するコンテナー イメージをプルする方法について説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

まず、[Anomaly Detector Containers Request フォーム](https://aka.ms/adcontainer)に入力して、送信し、コンテナーへのアクセスを要求する必要があります。

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>次のステップ

* コンテナー イメージのプルおよびコンテナーの実行の詳細については、[コンテナーのインストールおよび実行](../anomaly-detector-container-configuration.md)に関する説明を確認する
* 構成設定について、[コンテナーの構成](../anomaly-detector-container-configuration.md)を確認する
* [Anomaly Detector API サービスの詳細情報](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
