---
title: Azure Cognitive Search Service 대한 IP 방화벽 구성하기
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search Service에 대한 액세스를 제한하도록 IP 제어 정책을 구성합니다.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: de34c2921c7829cb6d7e7354a1ebcff44271efd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545550"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 IP 방화벽 구성하기

Azure Cognitive Search는 인바운드 방화벽에 대한 IP 규칙을 지원합니다. 해당 모델은 검색 서비스에 대하여 Azure 가상 네트워크 보안 그룹의 IP 규칙과 비슷한 추가 보안 계층을 제공합니다. 해당 IP 규칙을 사용하여 승인된 머신 또는 클라우드 서비스에만 액세스할 수 있도록 검색 서비스를 구성할 수 있습니다. 승인된 머신과 서비스에서 검색 서비스에 저장된 데이터에 액세스하려면 호출자가 유효한 권한 부여 토큰을 제공해야 합니다.

이 문서에 설명된 대로 Azure Portal에서 IP 규칙을 설정할 수 있습니다. 또는 [Management REST API version 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)를 사용할 수 있습니다.

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Azure Portal을 사용하여 IP 방화벽 구성하기

Azure Portal에서 IP 액세스 제어 정책을 설정하려면 Azure Cognitive Search 서비스 계정 페이지로 이동하고 탐색 메뉴에서 **네트워킹** 을 선택합니다. 엔드포인트 네트워킹 연결은 **퍼블릭** 이어야 합니다. 연결이 **프라이빗** 으로 설정된 경우 프라이빗 엔드포인트를 통해서만 검색 서비스에 액세스할 수 있습니다.

![Azure Portal에서 IP 방화벽 설정을 구성하는 방법을 보여 주는 스크린샷](./media/service-configure-firewall/azure-portal-firewall.png)

Azure Portal은 CIDR 형식으로 IP 주소와 IP 주소 범위를 지정할 수 있습니다. CIDR 표기법의 예로는 8.8.8.0/24가 있습니다. 이는 8.8.8.0에서 8.8.8.255까지 범위의 IP를 나타냅니다.

> [!NOTE]
> Azure Cognitive Search 서비스에서 IP 액세스 제어 정책을 사용하도록 설정한 후, 허용되는 IP 주소 범위의 허용 목록에 없는 머신의 데이터 평면에 대한 요청을 할 경우 요청은 거부됩니다. IP 규칙을 구성하면 Azure Portal의 일부 기능을 사용할 수 없습니다. 서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서, 기술 세트 정의와 같은 서비스의 다양한 구성 요소 및 인덱스 데이터에 대한 포털의 액세스는 보안상의 이유로 제한됩니다. 포털 대신 [VS Code 확장](https://aka.ms/vscode-search)을 사용하여 서비스의 다양한 구성 요소와 상호 작용할 수 있습니다.

### <a name="requests-from-your-current-ip"></a>현재 IP의 요청

개발을 간소화하기 위해 Azure Portal을 통해 클라이언트 머신의 IP를 식별하여 허용된 목록에 추가할 수 있습니다. 그러면 머신에서 실행되는 앱이 Azure Cognitive Search 서비스에 액세스할 수 있습니다.

포털에서 클라이언트 IP 주소를 자동으로 검색합니다. 이는 머신 또는 네트워크 게이트웨이의 클라이언트 IP 주소일 수 있습니다. 프로덕션에 대한 워크로드를 수행하기 전에 이 IP 주소를 제거해야 합니다.

현재 IP를 IP 목록에 추가하려면 **클라이언트 IP 주소 추가** 를 선택합니다. 그런 다음 **저장** 을 선택합니다.

![현재 IP를 허용하는 IP 방화벽 설정 구성 방법을 보여 주는 스크린샷](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP 액세스 제어 정책 관련 문제 해결

다음 옵션을 사용하여 IP 액세스 제어 정책 관련 문제를 해결할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Cognitive Search 서비스에 IP 액세스 제어 정책을 사용하도록 설정하면, Azure Portal을 포함하여 허용되는 IP 주소 범위 목록 외의 머신이 하는 모든 요청이 차단됩니다.  서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서, 기술 세트 정의와 같은 서비스의 다양한 구성 요소 및 인덱스 데이터에 대한 포털의 액세스는 보안상의 이유로 제한됩니다. 

### <a name="sdks"></a>SDK

허용 목록에 없는 머신에서 SDK를 사용하여 Azure Cognitive Search 서비스에 액세스하는 경우, 일반적인 **403 사용 권한 없음** 추가 세부 정보 없이 응답이 반환됩니다. 계정에 대해 허용된 IP 목록을 확인하고 검색 서비스에 올바른 구성이 업데이트되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Private Link를 통해 검색 서비스에 액세스하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cognitive Search에 보안 연결을 위한 프라이빗 엔드포인트 만들기](service-create-private-endpoint.md)