---
title: 전송 중 Azure HDInsight 암호화
description: Azure HDInsight 클러스터에 대한 전송 중 암호화를 제공하는 보안 기능에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946852"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Azure HDInsight의 전송 중 IPSec 암호화

이 문서에서는 Azure HDInsight 클러스터 노드 간 통신을 위해 전송 중 암호화를 구현하는 방법에 대해 설명합니다.

## <a name="background"></a>배경

Azure HDInsight는 엔터프라이즈 데이터를 보호하기 위한 다양한 보안 기능을 제공합니다. 해당 솔루션은 경계 보안, 인증, 권한 부여, 감사, 암호화 및 규정 준수의 핵심 요소로 그룹화되어 있습니다. 암호화는 미사용 데이터와 전송 중 데이터 모두에 적용할 수 있습니다.

미사용 암호화는 HDInsight 클러스터의 일부인 Azure VM의 디스크 암호화뿐만 아니라 Azure Storage 계정에서 서버 쪽 암호화를 통해 적용됩니다.

HDInsight에서 전송 중 데이터의 암호화는 클러스터 게이트웨이에 액세스하기 위한 [TLS(전송 계층 보안)](../transport-layer-security.md)와 클러스터 노드 간의 [IPSec(인터넷 프로토콜 보안)](https://wikipedia.org/wiki/IPsec)를 통해 이루어집니다. IPSec은 모든 헤드 노드, 작업자 노드, 에지 노드, zookeeper 노드, 게이트웨이 노드, [ID 브로커](./identity-broker.md) 노드 간에 선택적으로 사용할 수 있습니다.

## <a name="enable-encryption-in-transit"></a>전송 중 암호화 사용

### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 전송 중 암호화가 사용 설정된 새 클러스터를 만들려면 다음 단계를 수행합니다.

1. 클러스터를 만드는 정상적인 과정을 시작합니다. 처음 클러스터를 만드는 단계는 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.
1. **기본 사항** 및 **스토리지** 탭을 완료합니다. **보안 + 네트워킹** 탭으로 이동합니다.

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="클러스터 - 보안 및 네트워킹 탭을 만듭니다.":::

1. **보안 + 네트워킹** 탭에서 **전송 중 암호화 사용** 확인란을 선택합니다.

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="클러스터 만들기 - 전송 중 암호화 사용":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Azure CLI를 통해 전송 중 암호화가 사용 설정된 클러스터 만들기

전송 중 암호화는 `isEncryptionInTransitEnabled` 속성을 사용하여 사용 설정됩니다.

[샘플 템플릿 및 매개 변수 파일을 다운로드](https://github.com/Azure-Samples/hdinsight-enterprise-security)할 수 있습니다. 아래 템플릿 및 Azure CLI 코드 조각을 사용하기 전에 다음 자리 표시자를 올바른 값으로 바꿉니다.

| 자리 표시자 | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 구독의 ID입니다. |
| `<RESOURCE_GROUP>` | 새 클러스터 및 스토리지 계정을 만들 리소스 그룹입니다. |
| `<STORAGEACCOUNTNAME>` | 클러스터와 함께 사용해야 하는 기존 스토리지 계정입니다. 이름은 `ACCOUNTNAME.blob.core.windows.net` 형태여야 합니다. |
| `<CLUSTERNAME>` | HDInsight 클러스터의 이름입니다. |
| `<PASSWORD>` | SSH 및 Ambari 대시보드를 사용하여 클러스터에 로그인할 때 선택한 암호입니다. |
| `<VNET_NAME>` | 클러스터를 배포할 가상 네트워크입니다. |

아래 코드 조각은 다음 초기 단계를 수행합니다.

1. Azure 계정에 로그인합니다.
1. 만들기 작업을 수행할 활성 구독을 설정합니다.
1. 새 배포 활동에 대한 새 리소스 그룹을 만듭니다.
1. 템플릿을 배포하여 새 클러스터를 만듭니다.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight의 엔터프라이즈 보안 개요](hdinsight-security-overview.md)
* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../disk-encryption.md)합니다.
