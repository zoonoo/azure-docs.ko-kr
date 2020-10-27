---
title: FHIR 용 Azure API에 대 한 개인 링크
description: 이 문서에서는 FHIR 서비스에 대 한 Azure API에 대 한 개인 끝점을 설정 하는 방법을 설명 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: 121ddcc013fbeef29c6d22cfbd273cfd3b8d3d98
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558946"
---
# <a name="configure-private-link"></a>개인 링크 구성

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

개인 링크를 사용 하면 가상 네트워크의 개인 IP 주소를 사용 하 여 안전 하 게 안전 하 게 연결 하는 네트워크 인터페이스인 개인 끝점을 통해 FHIR 용 Azure API에 액세스할 수 있습니다. 개인 링크를 사용 하 여 공용 DNS를 통하지 않고도 Vnet에서 자사 서비스로 안전 하 게 서비스에 액세스할 수 있습니다. 이 문서에서는 FHIR 용 Azure API에 대 한 개인 끝점을 만들고, 테스트 하 고, 관리 하는 방법을 안내 합니다.

## <a name="prerequisites"></a>필수 구성 요소

개인 끝점을 만들기 전에 먼저 만들어야 할 몇 가지 Azure 리소스가 있습니다.

- 리소스 그룹 – 가상 네트워크 및 개인 끝점을 포함 하는 Azure 리소스 그룹입니다.
- FHIR 용 Azure API – 개인 끝점 뒤에 배치할 FHIR 리소스입니다.
- Virtual Network – 클라이언트 서비스 및 개인 끝점이 연결 될 VNet입니다.

자세한 내용은 [개인 링크 설명서](https://docs.microsoft.com/azure/private-link/)를 참조 하세요.

## <a name="disable-public-network-access"></a>공용 네트워크 액세스 사용 안 함

FHIR 리소스에 대 한 개인 끝점을 만드는 경우 해당 리소스에 대 한 공용 트래픽은 자동으로 사용 하지 않도록 설정 됩니다. 이렇게 하려면 새 "공용 액세스" 속성을 "사용"에서 "사용 안 함"으로 설정 하도록 FHIR 리소스를 업데이트 해야 합니다. 올바르게 구성 된 개인 끝점에서 제공 하지 않는 FHIR 서비스에 대 한 모든 요청이 거부 되므로 공용 네트워크 액세스를 사용 하지 않도록 설정할 때 주의 해야 합니다. 개인 끝점의 트래픽만 허용 됩니다.

![공용 네트워크 액세스 사용 안 함](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>개인 끝점 만들기

FHIR 리소스에 대 한 RBAC 권한이 있는 개발자는 Azure Portal, [Azure PowerShell](https://docs.microsoft.com/azure/private-link/create-private-endpoint-powershell)또는 [Azure CLI](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli)를 사용할 수 있습니다. 이 문서에서는 Azure Portal를 사용 하는 단계를 안내 합니다. 사설 DNS 영역을 만들고 구성 하는 것을 자동화 하므로 Azure Portal를 사용 하는 것이 좋습니다. 자세한 내용은 [개인 링크 빠른 시작 가이드](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal) 를 참조할 수 있습니다.

두 가지 방법으로 개인 끝점을 만들 수 있습니다. 자동 승인 흐름을 사용 하면 FHIR 리소스에 대 한 RBAC 권한이 있는 사용자가 승인 없이 개인 끝점을 만들 수 있습니다. 수동 승인 흐름을 사용 하면 FHIR 리소스에 대 한 사용 권한이 없는 사용자가 FHIR 리소스 소유자가 승인할 개인 끝점을 요청할 수 있습니다.

### <a name="auto-approval"></a>자동 승인

새 개인 끝점의 지역이 Virtual Network의 지역과 동일한 지 확인 합니다. FHIR 리소스에 대 한 지역은 다를 수 있습니다.

![Azure Portal 기본 사항 탭](media/private-link/private-link-portal2.png)

리소스 종류에 대해 "HealthcareApis/services"를 검색 하 고 선택 합니다. 리소스에 대해 FHIR 리소스를 선택 합니다. 대상 하위 리소스에 대해 "fhir"를 선택 합니다.

![Azure Portal 리소스 탭](media/private-link/private-link-portal1.png)

기존 사설 DNS 영역을 설정 하지 않은 경우 "(New) privatelink. azurehealthcareapis"를 선택 합니다. 사설 DNS 영역을 이미 구성한 경우 목록에서 선택할 수 있습니다. "Privatelink.azurehealthcareapis.com" 형식 이어야 합니다.

![Azure Portal 구성 탭](media/private-link/private-link-portal3.png)

배포가 완료 되 면 "개인 끝점 연결" 탭으로 돌아갈 수 있습니다. 그러면 "승인 됨"이 연결 상태로 표시 됩니다.

### <a name="manual-approval"></a>수동 승인

수동 승인의 경우 리소스, "리소스 ID로 Azure 리소스에 연결"에서 두 번째 옵션을 선택 합니다. 대상 하위 리소스에 대해 자동 승인에서와 같이 "fhir"을 입력 합니다.

![수동 승인](media/private-link/private-link-manual.png)

배포가 완료 되 면 연결을 승인, 거부 또는 제거할 수 있는 "개인 끝점 연결" 탭으로 돌아갈 수 있습니다.

![옵션](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>테스트 개인 끝점

공용 네트워크 액세스를 사용 하지 않도록 설정한 후에도 FHIR 서버가 공용 트래픽을 수신 하지 않도록 하려면 컴퓨터에서 서버에 대 한/metadata 끝점을 방문 하세요. 403를 받을 수 없습니다. 공용 트래픽이 차단 되기 전에 공용 네트워크 액세스 플래그를 업데이트 한 후에는 최대 5 분이 걸릴 수 있습니다.

개인 끝점에서 서버로 트래픽을 보낼 수 있는지 확인 하려면 다음을 수행 합니다.

1. 개인 끝점이 구성 된 가상 네트워크 및 서브넷에 연결 된 VM을 만듭니다. VM의 트래픽이 개인 네트워크만 사용 하도록 하려면 NSG 규칙을 통해 아웃 바운드 인터넷 트래픽을 사용 하지 않도록 설정할 수 있습니다.
2. VM에 RDP.
3. VM에서 FHIR 서버의/metadata 끝점을 방문 하 여 기능 문을 응답으로 받아야 합니다.

## <a name="manage-private-endpoint"></a>프라이빗 엔드포인트 관리

### <a name="view"></a>보기

개인 끝점과 연결 된 NIC는 생성 된 리소스 그룹에서 Azure Portal 볼 수 있습니다.

![리소스에서 보기](media/private-link/private-link-view.png)

### <a name="delete"></a>삭제

개인 끝점은 개요 블레이드 (아래 참조)를 통하거나 네트워킹 (미리 보기)의 "개인 끝점 연결" 탭에서 삭제 옵션을 통해 Azure Portal 에서만 삭제할 수 있습니다. 삭제 단추를 클릭 하면 개인 끝점과 연결 된 NIC가 삭제 됩니다. FHIR 리소스에 대 한 모든 개인 끝점을 삭제 하 고 공용 네트워크 액세스를 사용 하지 않도록 설정한 경우 FHIR 서버에 요청 하지 않습니다. FHIR 리소스를 삭제 하거나 이동 하려면 먼저 모든 개인 끝점을 FHIR 리소스에서 삭제 해야 합니다.

![개인 끝점 삭제](media/private-link/private-link-delete.png)
