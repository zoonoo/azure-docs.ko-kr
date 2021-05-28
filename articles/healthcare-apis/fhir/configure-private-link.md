---
title: Azure API for FHIR 대한 프라이빗 링크
description: 이 문서에서는 Azure API for FHIR 서비스에 대한 프라이빗 엔드포인트를 설정하는 방법을 설명합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/27/2021
ms.author: zxue
ms.openlocfilehash: 6b025c35c5faa64a2333710693c992a410cafecc
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652726"
---
# <a name="configure-private-link"></a>프라이빗 링크 구성

프라이빗 링크를 사용하면 프라이빗 엔드포인트를 통해 Azure API for FHIR 액세스할 수 있습니다. 이 인터페이스는 가상 네트워크의 개인 IP 주소를 사용하여 개인 및 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 링크를 사용하면 공용 DNS(Domain Name System)를 거치지 않고도 VNet에서 안전하게 서비스에 액세스할 수 있습니다. 이 문서에서는 Azure API for FHIR 대한 프라이빗 엔드포인트를 만들고, 테스트하고, 관리하는 방법을 설명합니다.

>[!Note]
>Private Link 사용하도록 설정되면 Private Link 또는 Azure API for FHIR 리소스 그룹 또는 구독에서 다른 리소스 그룹으로 이동할 수 없습니다. 이동하려면 먼저 Private Link 삭제한 다음 Azure API for FHIR 이동합니다. 이동이 완료되면 새 Private Link 만듭니다. Private Link 삭제하기 전에 잠재적인 보안 결과를 평가합니다.
>
>감사 로그 및 메트릭 내보내기에서 Azure API for FHIR 사용하도록 설정된 경우 포털에서 **진단 설정을** 통해 내보내기 설정을 업데이트합니다.

## <a name="prerequisites"></a>필수 구성 요소

프라이빗 엔드포인트를 만들기 전에 먼저 만들어야 하는 몇 가지 Azure 리소스가 있습니다.

- 리소스 그룹 – 가상 네트워크 및 프라이빗 엔드포인트를 포함할 Azure 리소스 그룹입니다.
- Azure API for FHIR – 프라이빗 엔드포인트 뒤에 배치하려는 FHIR 리소스입니다.
- Virtual Network – 클라이언트 서비스와 프라이빗 엔드포인트가 연결될 VNet입니다.

자세한 내용은 [Private Link 설명서를 참조하세요.](../../private-link/index.yml)

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

프라이빗 엔드포인트를 만들기 위해 FHIR 리소스에 대한 RBAC(역할 기반 액세스 제어) 권한이 있는 개발자는 Azure Portal, [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)또는 [Azure CLI](../../private-link/create-private-endpoint-cli.md)사용할 수 있습니다. 이 문서에서는 Azure Portal 사용하는 단계를 안내합니다. 프라이빗 DNS 영역의 생성 및 구성을 자동화하기 때문에 Azure Portal 사용하는 것이 좋습니다. 자세한 내용은 [Private Link 빠른 시작 가이드를 참조하세요.](../../private-link/create-private-endpoint-portal.md)

두 가지 방법으로 개인 끝점을 만들 수 있습니다. 자동 승인 흐름을 사용 하면 FHIR 리소스에 대 한 RBAC 권한이 있는 사용자가 승인 없이 개인 끝점을 만들 수 있습니다. 수동 승인 흐름을 사용 하면 FHIR 리소스에 대 한 사용 권한이 없는 사용자가 FHIR 리소스 소유자가 승인할 개인 끝점을 요청할 수 있습니다.

> [!NOTE]
> Azure API for FHIR에 대해 승인 된 개인 끝점을 만들면 해당 끝점에 대 한 공용 트래픽이 자동으로 사용 하지 않도록 설정 됩니다. 

### <a name="auto-approval"></a>자동 승인

새 개인 끝점의 지역이 가상 네트워크의 지역과 동일한 지 확인 합니다. FHIR 리소스에 대 한 지역은 다를 수 있습니다.

![Azure Portal 기본 사항 탭](media/private-link/private-link-portal2.png)

리소스 종류에 대해 **HealthcareApis/services** 를 검색 하 고 선택 합니다. 리소스에 대해 FHIR 리소스를 선택 합니다. 대상 하위 리소스에 대해 **Fhir** 을 선택 합니다.

![Azure Portal 리소스 탭](media/private-link/private-link-portal1.png)

기존 사설 DNS 영역을 설정 하지 않은 경우 **(New) privatelink azurehealthcareapis** 를 선택 합니다. 사설 DNS 영역을 이미 구성한 경우 목록에서 선택할 수 있습니다. **Privatelink.azurehealthcareapis.com** 형식 이어야 합니다.

![Azure Portal 구성 탭](media/private-link/private-link-portal3.png)

배포가 완료 된 후에는 연결 상태로 **승인** 된 **개인 끝점 연결** 탭으로 돌아갈 수 있습니다.

### <a name="manual-approval"></a>수동 승인

수동 승인의 경우 리소스, "리소스 ID로 Azure 리소스에 연결"에서 두 번째 옵션을 선택 합니다. 대상 하위 리소스에 대해 자동 승인에서와 같이 "fhir"을 입력 합니다.

![수동 승인](media/private-link/private-link-manual.png)

배포가 완료되면 "프라이빗 엔드포인트 연결" 탭으로 돌아가서 연결을 승인, 거부 또는 제거할 수 있습니다.

![옵션](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>프라이빗 엔드포인트 테스트

공용 네트워크 액세스를 사용하지 않도록 설정하면 FHIR 서버가 공용 트래픽을 수신하지 않도록 하려면 컴퓨터에서 서버에 대한 /metadata 엔드포인트를 선택합니다. 403 사용할 수 없음이 수신됩니다. 


> [!NOTE]
> 공용 트래픽이 차단되기 전에 공용 네트워크 액세스 플래그를 업데이트한 후 최대 5분이 걸릴 수 있습니다.

프라이빗 엔드포인트가 서버에 트래픽을 보낼 수 있도록 하려면 다음을 수행합니다.

1. 프라이빗 엔드포인트가 구성된 가상 네트워크 및 서브넷에 연결된 VM(가상 머신)을 만듭니다. VM의 트래픽이 프라이빗 네트워크만 사용 중인지 확인하려면 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 인터넷 트래픽을 사용하지 않도록 설정합니다.
2. VM에 RDP합니다.
3. VM에서 FHIR 서버의 /metadata 엔드포인트에 액세스합니다. 응답으로 capability 문을 받아야 합니다.

## <a name="manage-private-endpoint"></a>프라이빗 엔드포인트 관리

### <a name="view"></a>보기

프라이빗 엔드포인트 및 연결된 NIC(네트워크 인터페이스 컨트롤러)는 만들어진 리소스 그룹의 Azure Portal 표시됩니다.

![리소스에서 보기](media/private-link/private-link-view.png)

### <a name="delete"></a>DELETE

프라이빗 엔드포인트는 **개요** 블레이드의 Azure Portal 또는 **네트워킹 프라이빗 엔드포인트 연결** 탭에서 **제거** 옵션을 선택하여 삭제할 수 있습니다. **제거를** 선택하면 프라이빗 엔드포인트 및 연결된 NIC가 삭제됩니다. FHIR 리소스 및 공용 네트워크에 대한 모든 프라이빗 엔드포인트를 삭제하면 액세스가 비활성화되고 FHIR 서버에 대한 요청이 없습니다.

![프라이빗 엔드포인트 삭제](media/private-link/private-link-delete.png)
