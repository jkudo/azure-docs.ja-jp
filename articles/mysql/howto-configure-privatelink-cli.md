---
title: Azure Database for MySQL (プレビュー) 用のプライベート リンクの CLI のセットアップ方法
description: Azure CLI から Azure Database for MySQL 用のプライベート リンクを構成する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 59c38423f771685dc79a8be12a383cfdec6a0266
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031528"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-cli"></a>CLI を使用して Azure Database for MySQL (プレビュー) 用のプライベート リンクを作成および管理する

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 この記事では、Azure CLI を使用して Azure Virtual Network 内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for MySQL サーバーを作成する方法について説明します。

> [!NOTE]
> この機能は、Azure Database for MySQL が汎用およびメモリ最適化の価格レベルをサポートしているすべての Azure リージョンで使用できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに Azure CLI をローカルにインストールして使用する場合は、このクイック スタートには Azure CLI バージョン 2.0.28 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソースを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*westeurope* の場所に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します
[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 この例では、*mySubnet* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>サブネットのプライベート エンドポイント ポリシーを無効にする 
Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントのネットワーク ポリシーを無効にするようにサブネットを作成または更新する必要があります。 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) を使用して  *mySubnet*  という名前のサブネット構成を更新します。

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM の作成 
az vm create を使用して VM を作成します。 メッセージが表示されたら、VM のサインイン資格情報として使用するパスワードを入力します。 この例では、*myVm* という名前の VM を作成します。 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
VM のパブリック IP アドレスを書き留めます。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成 
az mysql server create コマンドを使用して Azure Database for MySQL を作成します。 MySQL サーバーの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダー値を独自の一意の値に置き換えることを忘れないでください。 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

MySQL サーバー ID は  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` と同様であることに注意してください。この MySQL サーバー ID は次の手順で使用します。 

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する 
Virtual Network 内に MySQL サーバーのプライベート エンドポイントを作成します。 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
MySQL サーバー ドメイン用のプライベート DNS ゾーンを作成し、Virtual Network との関連付けリンクを作成します。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. ダウンロードした .rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>VM から MySQL サーバーにプライベートにアクセスする

1.  *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`」と入力します。 

    次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Connection Name| Select the connection name of your choice.|
    | Hostname | Select *mydemoserver.privatelink.mysql.database.azure.com* |
    | Username | Enter username as *username@servername* which is provided during the MySQL server creation. |
    | Password | Enter a password provided during the MySQL server creation. |
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL database.

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイントの概要](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)について学習します。
