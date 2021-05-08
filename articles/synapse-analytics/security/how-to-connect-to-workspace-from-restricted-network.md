---
title: 제한된 네트워크에서 Azure Synapse Analytics Studio 작업 영역 리소스에 연결
description: 이 문서에서는 제한된 네트워크에서 작업 영역 리소스에 연결하는 방법을 설명합니다.
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384523"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>제한된 네트워크에서 작업 영역 리소스에 연결

조직에서 제한된 네트워크를 관리하는 IT 관리자가 있다고 가정해 보겠습니다. Azure Synapse Analytics Studio와 제한된 네트워크 내 워크스테이션 사이에 네트워크 연결이 가능하도록 설정하려고 합니다. 이 문서에서 그 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Synapse Analytics 작업 영역**: Azure Synapse Analytics에서 만들 수 있습니다. 4단계에서는 작업 영역 이름이 필요합니다.
* **제한된 네트워크**: IT 관리자는 조직에서 제한된 네트워크를 유지하고 네트워크 정책을 구성할 수 있는 권한이 있습니다. 3단계에서 가상 네트워크 이름 및 서브넷이 필요합니다.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>1단계: 제한된 네트워크에 네트워크 아웃바운드 보안 규칙 추가하기

서비스 태그 4개를 사용하여 네트워크 아웃바운드 보안 규칙 4개를 추가해야 합니다. 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor(선택 규칙. Microsoft와 데이터를 공유하려는 경우에만 추가)

다음 스크린샷을 보면 Azure Resource Manager 아웃바운드 규칙에 대한 세부 정보를 알 수 있습니다.

![Azure Resource Manager 서비스 태그 세부 정보를 보여 주는 스크린샷.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

다른 규칙 3개를 만들 때 **대상 서비스 태그** 의 값을 목록에서 **AzureFrontDoor.Frontend**, **AzureActiveDirectory** 또는 **AzureMonitor** 로 바꿉니다.

자세한 내용은 [서비스 태그 개요](../../virtual-network/service-tags-overview.md)를 참조하세요.

## <a name="step-2-create-private-link-hubs"></a>2단계: 프라이빗 링크 허브 만들기

다음으로 Azure Portal에서 프라이빗 링크 허브를 만듭니다. 이를 포털에서 확인하기 위해서는 *Azure Synapse Analytics(프라이빗 링크 허브)* 를 검색한 뒤 필수 정보를 입력하여 만드세요. 

![Synapse 프라이빗 링크 허브 만들기 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>3단계: Synapse Studio에 프라이빗 엔드포인트 만들기

Azure Synapse Analytics Studio에 액세스하려면, Azure Portal에서 프라이빗 엔드포인트를 만들어야 합니다. 이를 포털에서 확인하려면, *프라이빗 링크* 를 검색하세요. **Private Link 센터** 에서 **프라이빗 엔드포인트 만들기** 를 선택한 다음, 필수 정보를 입력하여 만듭니다. 

> [!Note]
> **하위 지역** 값이 Azure Synapse Analytics 작업 영역에 있는 값과 동일한지 확인합니다.

![프라이빗 엔드포인트 만들기, 기본 사항 탭의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

**리소스** 탭에서 2단계에서 만든 프라이빗 링크 허브를 선택합니다.

![프라이빗 엔드포인트 만들기, 리소스 탭의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

**구성** 탭에서: 
* **가상 네트워크** 에서 제한된 가상 네트워크 이름을 선택합니다.
* **서브넷** 에서 제한된 가상 네트워크의 서브넷을 선택합니다. 
* **프라이빗 DNS 영역과 통합** 에서 **예** 를 선택합니다.

![프라이빗 엔드포인트 만들기, 구성 탭의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

프라이빗 링크 엔드포인트를 만든 후에는 Azure Synapse Analytics Studio용 웹 도구 로그인 페이지에 액세스할 수 있습니다. 하지만 아직 작업 영역 내 리소스에는 액세스할 수 없습니다. 액세스하기 위해서는 다음 단계를 완료해야 합니다.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>4단계: 작업 영역 리소스에 프라이빗 엔드포인트 만들기

Azure Synapse Analytics Studio 작업 영역 리소스 안에 있는 리소스에 액세스하려면 다음의 과정을 수행해야 합니다.

- **대상 하위 리소스** 종류 **Dev** 를 사용하여 프라이빗 링크 엔드포인트 한 개 이상을 만듭니다.
- 액세스하려는 작업 영역의 리소스에 따라 **Sql** 또는 **SqlOnDemand** 종류 선택하여 프라이빗 2개의 링크 엔드포인트를 더 만듭니다.

만드는 방법은 이전 단계에서 엔드포인트를 만드는 방법과 비슷합니다.  

**리소스** 탭에서 다음을 수행합니다.

* **리소스 종류** 에서 **Microsoft.Synapse/workspaces** 를 선택합니다.
* **리소스** 에서 이전에 만든 작업 영역 이름을 선택합니다.
* **대상 하위 리소스** 에서 엔드포인트 형식을 선택합니다.
  * **Sql** 는 SQL 풀에서 SQL 쿼리 실행 시 사용합니다.
  * **SqlOnDemand** 는 SQL 기본 제공 쿼리 실행 시 사용합니다.
  * **Dev** 는 Azure Synapse Analytics Studio 작업 영역 내에서 다른 모든 항목에 액세스할 때 사용합니다. Dev 형식 프라이빗 링크 엔드포인트를 하나 이상 만들어야 합니다.

![프라이빗 엔드포인트 만들기와 리소스 탭, 작업 영역의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>5단계: 작업 영역에 연결된 스토리지에 프라이빗 엔드포인트 만들기

Azure Synapse Analytics Studio 작업 영역에서 스토리지 탐색기를 사용하여 연결된 스토리지에 액세스하려면 프라이빗 엔드포인트 하나를 만들어야 합니다. 이에 대한 단계는 3단계와 유사합니다. 

**리소스** 탭에서 다음을 수행합니다.
* **리소스 종류** 에서 **Microsoft.Synapse/storageAccounts** 를 선택합니다.
* **리소스** 에서 이전에 만든 스토리지 계정 이름을 선택합니다.
* **대상 하위 리소스** 에서 엔드포인트 형식을 선택합니다.
  * **blob** 은 Azure Blob Storage에 사용합니다.
  * **dfs** 는 Azure Data Lake Storage Gen2에 사용합니다.

![프라이빗 엔드포인트 만들기, 리소스 탭, 스토리지의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

이제 연결된 스토리지 리소스에 액세스할 수 있습니다. 가상 네트워크 내의 Azure Synapse Analytics Studio 작업 영역에서 스토리지 탐색기를 사용하여 연결된 스토리지 리소스에 액세스할 수 있습니다.

다음 스크린샷에서 볼 수 있듯이 작업 영역에서 관리형 가상 네트워크를 사용하도록 설정할 수 있습니다.

![관리형 가상 네트워크 사용 옵션이 강조 표시된 Synapse 작업 영역 만들기의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

노트북에서 특정 스토리지 계정을 사용하여 연결된 스토리지 리소스에 액세스하려면 Azure Synapse Analytics Studio에서 관리형 프라이빗 엔드포인트를 추가합니다. 스토리지 계정 이름은 노트북에서 액세스하려는 이름이어야 합니다. 자세한 내용은 [데이터 원본에 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)를 참조하세요.

엔드포인트를 만든 후 승인 상태는 **보류 중** 으로 표시됩니다. Azure Portal의 스토리지 계정에 있는 **프라이빗 엔드포인트 연결** 탭에서 스토리지 계정 소유자에게 승인을 요청합니다. 승인이 되면 노트북은 스토리지 계정을 써서 연결된 스토리지 리소스에 액세스할 수 있습니다.

이제 준비가 끝났습니다. Azure Synapse Analytics Studio 작업 영역 리소스에 액세스할 수 있습니다.

## <a name="appendix-dns-registration-for-private-endpoint"></a>부록: 프라이빗 엔드포인트에 DNS 등록하기

아래 스크린샷에서 볼 수 있듯이 프라이빗 엔드포인트 생성 중 ‘프라이빗 DNS 영역 통합’을 사용하도록 설정되지 않은 경우, 각 프라이빗 엔드포인트에 대해 ‘**프라이빗 DNS 영역**’을 만들어야 합니다.
![Synapse 프라이빗 DNS 영역 만들기 스크린샷 1.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

포털에서 **프라이빗 DNS 영역** 을 찾으려면, *프라이빗 DNS 영역* 을 검색합니다. **프라이빗 DNS 영역** 에서 아래 필수 정보를 입력하여 만듭니다.

* **이름** 에 아래와 같이 특정 프라이빗 엔드포인트에 대한 프라이빗 DNS 영역 전용 이름을 입력합니다.
  * **`privatelink.azuresynapse.net`** 은 Azure Synapse Analytics Studio 게이트웨이에 액세스하는 프라이빗 엔드포인트에 쓰입니다. 해당 형식의 프라이빗 엔드포인트를 만들려면 3단계를 참조하세요.
  * **`privatelink.sql.azuresynapse.net`** 은 SQL 풀 및 기본 제공 풀에서 SQL 쿼리 실행용 프라이빗 엔드포인트에 쓰입니다. 해당 형식의 엔드포인트를 만들려면 4단계를 참조하세요.
  * **`privatelink.dev.azuresynapse.net`** 은 Azure Synapse Analytics Studio 작업 영역 내에서 다른 모든 항목에 액세스하는 프라이빗 엔드포인트에 쓰입니다. 해당 형식의 프라이빗 엔드포인트를 만들려면 4단계를 참조하세요.
  * **`privatelink.dfs.core.windows.net`** 은 Azure Data Lake Storage Gen2에 연결된 작업 영역에 액세스하는 프라이빗 엔드포인트에 쓰입니다. 해당 형식의 프라이빗 엔드포인트를 만들려면 5단계를 참조하세요.
  * **`privatelink.blob.core.windows.net`** 는 Azure Blob Storage에 연결된 작업 영역에 액세스하는 프라이빗 엔드포인트에 쓰입니다. 해당 형식의 프라이빗 엔드포인트를 만들려면 5단계를 참조하세요.

![Synapse 프라이빗 DNS 영역 만들기 스크린샷 2.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

**프라이빗 DNS 영역** 을 만든 후 생성된 프라이빗 DNS 영역을 입력하고 **가상 네트워크 링크** 를 선택하여 가상 네트워크에 링크를 추가합니다. 

![Synapse 프라이빗 DNS 영역 만들기 스크린샷 3.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

아래와 같이 필수 정보를 입력합니다.
* **링크 이름** 에 링크 이름을 입력합니다.
* **가상 네트워크** 에서 가상 네트워크를 선택합니다.

![Synapse 프라이빗 DNS 영역 만들기 스크린샷 4.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

가상 네트워크 링크가 추가된 후 이전에 만든 **프라이빗 DNS 영역** 에 DNS 레코드 집합을 추가해야 합니다.

* **이름** 에 다른 프라이빗 엔드포인트에 대한 전용 이름 문자열을 입력합니다. 
  * **웹** 은 Azure Synapse Analytics Studio 액세스용 프라이빗 엔드포인트에 사용합니다.
  * ‘***YourWorkSpaceName***’은 SQL 풀에서 SQL 쿼리 실행용 프라이빗 엔드포인트와 Azure Synapse Analytics Studio 작업 영역 내 다른 모든 항목에 액세스하는 프라이빗 엔드포인트에 쓰입니다.
  * ‘**YourWorkSpaceName-ondemand**’는 기본 제공 풀에서 SQL 쿼리 실행용 프라이빗 엔드포인트에 쓰입니다.
* **형식** 에서 DNS 레코드 **A** 형만 선택합니다. 
* **IP 주소** 에서 각 프라이빗 엔드포인트의 해당 IP 주소를 입력합니다. 프라이빗 엔드포인트 개요의 **네트워크 인터페이스** 에서 IP 주소를 가져올 수 있습니다.

![Synapse 프라이빗 DNS 영역 만들기 스크린샷 5.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>다음 단계

[관리형 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대한 자세한 정보.

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보.
