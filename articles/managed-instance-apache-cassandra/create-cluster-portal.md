---
title: 빠른 시작 - Azure Portal에서 Apache Cassandra 클러스터용 Azure Managed Instance 생성
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 생성하는 방법을 설명합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: a05769c66c4b13de5c7197ef5612d64781574987
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747908"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>빠른 시작: Azure Portal에서 Apache Cassandra 클러스터용 Azure Managed Instance 생성(미리 보기)
 
Apache Cassandra용 Azure Managed Instance는 관리형 오픈 소스 Apache Cassandra 데이터 센터에 대한 자동화된 배포 및 스케일링 작업을 제공하여 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄여줍니다.

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 빠른 시작에서는 Azure Portal을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>관리형 인스턴스 클러스터 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 검색 창에서 **Apache Cassandra용 Managed Instance** 를 검색하고 결과를 선택합니다.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Apache Cassandra용 Managed Instance를 검색합니다." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. **Apache Cassandra 클러스터용 Managed Instance 만들기** 단추를 선택합니다.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="클러스터를 만듭니다." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. **Apache Cassandra용 Managed Instance 만들기** 창에서 다음 세부 정보를 입력합니다.

   * **구독** - 드롭다운에서 Azure 구독을 선택합니다.
   * **리소스 그룹** - 새 리소스 그룹을 만들지 또는 기존 집합을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요 문서를 참조하세요.
   * **클러스터 이름** - 클러스터의 이름을 입력합니다.
   * **위치** - 클러스터가 배포되는 위치입니다.
   * **SKU** - 클러스터의 SKU 형식입니다.
   * **노드 수** - 클러스터의 노드 수입니다. 이러한 노드는 데이터의 복제본 역할을 합니다.
   * **최초 Cassandra 관리자 암호** - 클러스터를 만드는 데 사용되는 암호입니다.
   * **Cassandra 관리자 암호 확인** - 암호를 다시 입력합니다.

    > [!NOTE]
    > 공개 미리 보기 중에는 미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중남부, 북유럽, 서유럽, 동남 아시아, 오스트레일리아 동부 지역에서 관리형 인스턴스 클러스터를 만들 수 있습니다.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="클러스터 만들기 양식을 작성합니다." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. 다음으로 **네트워킹** 탭을 선택합니다.

1. **네트워킹** 창에서 **가상 네트워크** 이름과 **서브넷** 을 선택합니다. 기존 가상 네트워크를 선택하거나 새로 만들 수 있습니다.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="네트워킹 세부 정보를 구성합니다." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. 마지막 단계에서 새 VNet을 만든 경우 9단계로 건너뜁니다. 기존 VNet을 선택한 경우 클러스터를 만들기 전에 가상 네트워크 및 서브넷에 몇 가지 특수 권한을 적용해야 합니다. 이를 위해 기존 가상 네트워크의 리소스 ID를 가져와야 합니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)에서 다음 명령 실행:

   ```azurecli-interactive
   # get the resource ID of the Virtual Network
   az network vnet show -n <VNet_name> -g <Resource_Group_Name> --query "id" --output tsv

1. Now apply the special permissions by using the `az role assignment create` command. Replace `<Resource ID>` with the output of previous command:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```

   > [!NOTE]
   > 이전 명령에서 `assignee`와 `role` 값은 각각 고정된 서비스 주체 및 역할 식별자입니다.

1. 이제 네트워킹은 완료되었으므로 **검토 + 만들기** > **만들기** 를 클릭합니다.

    > [!NOTE]
    > 클러스터를 만드는 데 최대 15분이 소요될 수 있습니다.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="요약을 검토하고 클러스터를 만듭니다." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. 배포가 완료된 후 리소스 그룹을 확인하여 새로 만든 관리형 인스턴스 클러스터를 확인:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="클러스터를 만든 후의 개요 페이지." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. 클러스터 노드를 탐색하려면 클러스터를 만드는 데 사용한 가상 네트워크 창으로 이동하고 **개요** 창을 열어 확인합니다.

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="클러스터 리소스를 확인합니다." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>클러스터에 연결

Apache Cassandra용 Azure Managed Instance는 공용 IP 주소가 포함된 노드를 만들지 않으므로 새로 만든 Cassandra 클러스터에 연결하려면 VNet 내에 다른 리소스를 만들어야 합니다. 애플리케이션이나 Apache의 오픈 소스 쿼리 도구인 [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)가 포함된 가상 머신이 설치될 수 있습니다. [템플릿](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)을 사용하여 Ubuntu 가상 머신을 배포할 수 있습니다. 배포된 경우 SSH를 사용하여 가상 머신에 연결하고 아래 명령을 사용하여 CQLSH를 설치합니다.

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>리소스 정리

관리형 인스턴스 클러스터를 계속 사용하지 않을 경우 다음 단계에 따라 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 목록에서 이 빠른 시작에서 만든 리소스 그룹을 선택합니다.
1. 리소스 그룹 **개요** 창에서 **리소스 그룹 삭제** 를 선택합니다.
1. 새 창에서 삭제할 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 Azure Portal을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 생성하는 방법을 알아보았습니다. 이제 클러스터 사용을 시작할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
