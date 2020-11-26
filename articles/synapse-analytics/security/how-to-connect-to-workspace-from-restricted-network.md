---
title: 제한 된 네트워크에서 Azure Synapse Analytics 스튜디오의 작업 영역 리소스에 연결
description: 이 문서에서는 제한 된 네트워크에서 작업 영역 리소스에 연결 하는 방법을 설명 합니다.
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 2e96cbf0c1464e27b0a384e8a813118056103b91
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296688"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>제한 된 네트워크에서 작업 영역 리소스에 연결

조직의 제한 된 네트워크를 관리 하는 IT 관리자가 있다고 가정해 보겠습니다. Azure Synapse Analytics Studio와이 제한 된 네트워크 내의 워크스테이션 사이에서 네트워크 연결을 사용 하도록 설정 하려고 합니다. 이 문서에서 그 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Synapse analytics 작업 영역**: Azure Synapse analytics에서 만들 수 있습니다. 4 단계에서 작업 영역 이름이 필요 합니다.
* **제한 된 네트워크**: IT 관리자는 조직에 대해 제한 된 네트워크를 유지 관리 하 고 네트워크 정책을 구성할 수 있는 권한을 가집니다. 3 단계에서 가상 네트워크 이름 및 서브넷이 필요 합니다.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>1 단계: 제한 된 네트워크에 네트워크 아웃 바운드 보안 규칙 추가

4 개의 서비스 태그를 사용 하 여 4 개의 네트워크 아웃 바운드 보안 규칙을 추가 해야 합니다. 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (이 유형의 규칙은 선택 사항입니다. Microsoft와 데이터를 공유 하려는 경우에만 추가 합니다.

다음 스크린샷은 Azure Resource Manager 아웃 바운드 규칙에 대 한 세부 정보를 보여줍니다.

![Azure Resource Manager 서비스 태그 정보에 대 한 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

다른 세 가지 규칙을 만드는 경우 **대상 서비스 태그** 의 값을 목록에서 **AzureFrontDoor**, **AzureActiveDirectory** 또는 **azuremonitor** 로 바꿉니다.

자세한 내용은 [서비스 태그 개요](/azure/virtual-network/service-tags-overview)를 참조 하세요.

## <a name="step-2-create-private-link-hubs"></a>2 단계: 개인 링크 허브 만들기

다음으로 Azure Portal에서 개인 링크 허브를 만듭니다. 포털에서이를 확인 하려면 *Azure Synapse Analytics (개인 링크 허브)* 를 검색 한 다음 필요한 정보를 입력 하 여 만듭니다. 

![Synapse 개인 링크 허브 만들기의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>3 단계: Synapse Studio에 대 한 개인 끝점 만들기

Azure Synapse Analytics Studio에 액세스 하려면 Azure Portal에서 개인 끝점을 만들어야 합니다. 포털에서이를 찾으려면 *개인 링크* 를 검색 합니다. **개인 링크 센터** 에서 **개인 끝점 만들기** 를 선택한 다음 필요한 정보를 입력 하 여 만듭니다. 

> [!Note]
> **지역** 값이 Azure Synapse Analytics 작업 영역이 있는 것과 동일한 지 확인 합니다.

![개인 끝점 만들기, 기본 사항 탭의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

**리소스** 탭에서 2 단계에서 만든 개인 링크 허브를 선택 합니다.

![개인 끝점 만들기, 리소스 탭의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

**구성** 탭에서 다음을 수행 합니다. 
* **가상 네트워크** 에 대해 제한 된 가상 네트워크 이름을 선택 합니다.
* **서브넷** 에 대해 제한 된 가상 네트워크의 서브넷을 선택 합니다. 
* **개인 DNS 영역과 통합** 하는 경우 **예** 를 선택 합니다.

![개인 끝점 만들기, 구성 탭의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

개인 링크 끝점이 생성 된 후에는 Azure Synapse Analytics Studio 용 웹 도구의 로그인 페이지에 액세스할 수 있습니다. 그러나 아직 작업 영역 내의 리소스에는 액세스할 수 없습니다. 이렇게 하려면 다음 단계를 완료 해야 합니다.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>4 단계: 작업 영역 리소스에 대 한 개인 끝점 만들기

Azure Synapse Analytics Studio 작업 영역 리소스 내의 리소스에 액세스 하려면 다음을 만들어야 합니다.

- **개발자** 형식의 **대상 하위 리소스** 를 사용 하는 하나 이상의 개인 링크 끝점입니다.
- 액세스 하려는 작업 영역에 있는 리소스에 따라 **Sql** 또는 **SqlOnDemand** 유형을 포함 하는 두 개의 다른 선택적 개인 링크 끝점입니다.

이러한 만들기는 이전 단계에서 끝점을 만드는 방법과 비슷합니다.  

**리소스** 탭에서 다음을 수행 합니다.

* **리소스 종류** 에 대해 **Synapse/작업 영역** 을 선택 합니다.
* **리소스** 에 대해 이전에 만든 작업 영역 이름을 선택 합니다.
* **대상 하위 리소스** 에 대해 끝점 형식을 선택 합니다.
  * Sql은 sql 풀에서 SQL **쿼리를 실행** 하기 위한 것입니다.
  * **SqlOnDemand** 은 SQL 기본 제공 쿼리 실행에 대 한 것입니다.
  * **Dev** 는 Azure Synapse Analytics Studio 작업 영역 내에서 다른 모든 항목에 액세스 하는 데 사용할 수 있습니다. 이 형식의 개인 링크 끝점을 하나 이상 만들어야 합니다.

![개인 끝점 만들기, 리소스 탭, 작업 영역의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>5 단계: 작업 영역 연결 된 저장소에 대 한 개인 끝점 만들기

Azure Synapse Analytics Studio 작업 영역에서 저장소 탐색기를 사용 하 여 연결 된 저장소에 액세스 하려면 하나의 개인 끝점을 만들어야 합니다. 이에 대 한 단계는 3 단계와 유사 합니다. 

**리소스** 탭에서 다음을 수행 합니다.
* **리소스 종류** 에 대해 **Synapse/storageaccounts** 를 선택 합니다.
* **리소스** 에 대해 이전에 만든 저장소 계정 이름을 선택 합니다.
* **대상 하위 리소스** 에 대해 끝점 형식을 선택 합니다.
  * Azure Blob Storage에 대 한 **blob** 입니다.
  * **dfs** 는 Azure Data Lake Storage Gen2 용입니다.

![개인 끝점 만들기, 리소스 탭, 저장소의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

이제 연결 된 저장소 리소스에 액세스할 수 있습니다. 가상 네트워크 내의 Azure Synapse Analytics Studio 작업 영역에서 저장소 탐색기를 사용 하 여 연결 된 저장소 리소스에 액세스할 수 있습니다.

다음 스크린샷에 표시 된 것 처럼 작업 영역에 대 한 관리 되는 가상 네트워크를 사용 하도록 설정할 수 있습니다.

![관리 되는 가상 네트워크 사용 옵션이 강조 표시 된 Synapse 작업 영역 만들기의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

노트북이 특정 저장소 계정에서 연결 된 저장소 리소스에 액세스 하도록 하려면 Azure Synapse Analytics Studio 아래에 관리 되는 개인 끝점을 추가 합니다. 저장소 계정 이름은 노트북에서 액세스 해야 하는 이름 이어야 합니다. 자세한 내용은 [데이터 원본에 대 한 관리 되는 개인 끝점 만들기](./how-to-create-managed-private-endpoints.md)를 참조 하세요.

이 끝점을 만든 후 승인 상태는 **보류** 중 상태를 표시 합니다. Azure Portal에서이 저장소 계정의 **개인 끝점 연결** 탭에 있는이 저장소 계정의 소유자에 게 서 승인을 요청 합니다. 승인 되 면 노트북은이 저장소 계정으로 연결 된 저장소 리소스에 액세스할 수 있습니다.

이제 모든 집합이 있습니다. Azure Synapse Analytics Studio 작업 영역 리소스에 액세스할 수 있습니다.

## <a name="appendix-dns-registration-for-private-endpoint"></a>부록: 개인 끝점에 대 한 DNS 등록

아래 스크린샷에서 개인 끝점을 만드는 동안 "개인 DNS 영역 통합"을 사용 하도록 설정 하지 않은 경우 각 개인 끝점에 대해 "**사설 DNS 영역**"을 만들어야 합니다.
![Synapse 개인 DNS 영역 만들기 1의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

포털에서 **사설 DNS 영역** 을 찾으려면 *사설 DNS 영역* 을 검색 합니다. **사설 DNS 영역** 에서 아래 필요한 정보를 입력 하 여 만듭니다.

* **이름** 에 대해 아래와 같이 특정 개인 끝점의 전용 DNS 영역 전용 이름을 입력 합니다.
  * **`privatelink.azuresynapse.net`** 는 Azure Synapse Analytics Studio 게이트웨이에 액세스 하는 개인 끝점에 대 한입니다. 3 단계에서 이러한 형식의 개인 끝점 만들기를 참조 하세요.
  * **`privatelink.sql.azuresynapse.net`** 는 SQL 풀 및 기본 제공 풀에서 sql 쿼리 실행의 이러한 형식의 전용 끝점에 대 한 것입니다. 4 단계에서 끝점 만들기를 참조 하세요.
  * **`privatelink.dev.azuresynapse.net`** 는 Azure Synapse Analytics Studio 작업 영역 내에서 다른 모든 항목에 액세스 하는 이러한 유형의 개인 끝점에 대 한 것입니다. 4 단계에서 이러한 형식의 개인 끝점 만들기를 참조 하세요.
  * **`privatelink.dfs.core.windows.net`** 는 Azure Data Lake Storage Gen2 연결 된 작업 영역에 액세스 하는 전용 끝점에 대 한입니다. 5 단계에서 이러한 형식의 개인 끝점 만들기를 참조 하세요.
  * **`privatelink.blob.core.windows.net`** 는 Azure Blob Storage 연결 된 작업 영역에 액세스 하는 전용 끝점에 대 한입니다. 5 단계에서 이러한 형식의 개인 끝점 만들기를 참조 하세요.

![Synapse 개인 DNS 영역 만들기 2의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

**사설 DNS 영역** 을 만든 후에는 생성 된 개인 DNS 영역을 입력 하 고 **가상 네트워크 링크** 를 선택 하 여 가상 네트워크에 대 한 링크를 추가 합니다. 

![Synapse 개인 DNS 영역 만들기 3의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

다음과 같이 필수 필드를 입력 합니다.
* **링크 이름** 에 링크 이름을 입력 합니다.
* **가상 네트워크** 에서 가상 네트워크를 선택 합니다.

![Synapse 개인 DNS 영역 4 만들기의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

가상 네트워크 링크가 추가 된 후 이전에 만든 **사설 DNS 영역** 에 DNS 레코드 집합을 추가 해야 합니다.

* **이름** 에 대해 다른 개인 끝점의 전용 이름 문자열을 입력 합니다. 
  * **웹** 은 Azure Synapse Analytics Studio 액세스를 위한 개인 끝점입니다.
  * "***YourWorkSpaceName * * _"는 sql 풀에서의 sql 쿼리 실행 전용 끝점과 Azure Synapse Analytics Studio 작업 영역 내 다른 모든 항목에 액세스 하는 전용 끝점에 대 한 것입니다. _ "*** YourWorkSpaceName *-ondemand * *"는 기본 제공 풀에서 sql 쿼리 실행의 개인 끝점에 대 한 것입니다.
* **유형** 에 대해 DNS 레코드 유형 **A** 만을 선택 합니다. 
* **Ip 주소** 에 대해 각 개인 끝점의 해당 ip 주소를 입력 합니다. 개인 끝점 개요에서 **네트워크 인터페이스** 의 IP 주소를 가져올 수 있습니다.

![Synapse 개인 DNS 영역 만들기 5의 스크린샷](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>다음 단계

[관리 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대해 자세히 알아보세요.

[관리 되는 개인 끝점](./synapse-workspace-managed-private-endpoints.md)에 대해 자세히 알아보세요.
