---
title: 부서의 범위에 안전 하 게 액세스 하기 위해 개인 끝점 사용
description: 이 문서에서는 부서의 범위 계정에 대 한 개인 끝점을 설정 하는 방법을 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: d9088e5c6302c41c64f2a2e9034e7c3d659e37eb
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615638"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>부서의 범위 계정에 대 한 개인 끝점 사용

부서의 범위 계정에 대 한 개인 끝점을 사용 하 여 VNet (가상 네트워크)의 클라이언트 및 사용자가 개인 링크를 통해 카탈로그에 안전 하 게 액세스할 수 있도록 할 수 있습니다. 개인 끝점은 부서의 범위 계정에 대 한 VNet 주소 공간의 IP 주소를 사용 합니다. VNet과 부서의 범위 계정의 클라이언트 간의 네트워크 트래픽은 VNet을 통해 이동 하 여 공용 인터넷에서 노출 되는 것을 제거 하는 Microsoft 백본 네트워크의 개인 링크를 통해 이동 합니다.

## <a name="create-purview-account-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 부서의 범위 계정 만들기

1. [Azure Portal](https://portal.azure.com) 으로 이동한 다음 부서의 범위 계정으로 이동 합니다.

1. 기본 정보를 채우고 **네트워킹** 탭에서 연결 방법을 개인 끝점으로 설정 합니다. 개인 끝점과 페어링 하려는 **구독, Vnet 및 서브넷** 에 대 한 세부 정보를 제공 하 여 수집 개인 끝점을 설정 합니다.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Azure Portal에서 개인 끝점을 만듭니다.":::

1. 필요에 따라 각 수집 개인 끝점에 대 한 **사설 DNS 영역** 을 설정 하도록 선택할 수도 있습니다.

1. 추가를 클릭 하 여 부서의 범위 계정에 대 한 개인 끝점을 추가 합니다.

1. 개인 끝점 만들기 페이지에서 부서의 범위 하위 리소스를 **계정** 으로 설정 하 고, 가상 네트워크 및 서브넷을 선택 하 고, dns를 등록할 사설 DNS 영역을 선택 합니다. 또한 사용자의 가상 컴퓨터에서 호스트 파일을 사용 하 여 해당 dns 서버를 활용 하거나 dns 레코드를 만들 수 있습니다.

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="개인 끝점 만들기 선택 항목":::

1. **확인** 을 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="계정을 만들기 위해 유효성 검사를 통과 했습니다.":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>부서의 범위 웹 포털에 대 한 개인 끝점 만들기

1. 방금 만든 부서의 범위 계정으로 이동 하 고 설정 섹션에서 개인 끝점 연결을 선택 합니다.

1. + 개인 끝점을 클릭 하 여 새 개인 끝점을 만듭니다.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="포털 개인 끝점 만들기":::

1. 기본 정보를 입력 합니다.

1. 리소스 탭에서 리소스 유형을 **부서의 범위/accounts** 로 선택 합니다.

1. 새로 만든 부서의 범위 계정으로 사용할 리소스를 선택 하 고 대상 하위 리소스를 **포털로** 선택 합니다.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="포털 개인 끝점에 대 한 세부 정보":::

1. 구성 탭에서 가상 네트워크 및 사설 DNS 영역을 선택 합니다. 요약 페이지로 이동 하 고 **만들기** 를 클릭 하 여 포털 개인 끝점을 만듭니다.

## <a name="enabling-access-to-azure-active-directory"></a>Azure Active Directory에 대 한 액세스 사용

> [!NOTE]
> VM, VPN gateway 또는 VNET 피어 링 게이트웨이에서 공용 인터넷 액세스가 있는 경우 개인 끝점에서 사용 하도록 설정 된 부서의 범위 portal 및 부서의 범위 계정에 액세스할 수 있으며 아래 지침의 나머지를 따를 필요가 없습니다. 개인 네트워크에 모든 공용 인터넷 트래픽 거부로 설정 된 네트워크 보안 그룹 규칙이 있는 경우 AAD 액세스를 사용 하도록 설정 하는 몇 가지 규칙을 추가 해야 합니다. 이렇게 하려면 아래 지침을 따르세요.

아래 지침은 Azure VM에서 부서의 범위에 안전 하 게 액세스 하기 위한 것입니다. VPN 또는 다른 Vnet 피어 링 게이트웨이를 사용 하는 경우에도 유사한 단계를 수행 해야 합니다.

1. Azure Portal에서 VM으로 이동 하 고 설정 섹션에서 네트워킹 탭을 선택 합니다. 아웃 바운드 포트 규칙을 선택 하 고 아웃 바운드 포트 규칙 추가를 클릭 합니다.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="아웃 바운드 규칙 추가":::

2. 아웃 바운드 포트 규칙 추가 블레이드에서 *대상* 으로 지정할 대상 서비스 태그, 대상 서비스 태그를 **AzureActiveDirectory**, 대상 포트 범위를 *로 지정 합니다. 허용 되는 작업, **우선 순위는 모든 인터넷 트래픽을 거부 한 규칙 보다 높아야 합니다**. 규칙을 만듭니다.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="아웃 바운드 규칙 추가 정보":::

3. 동일한 단계에 따라 '**AzureResourceManager**' 서비스 태그를 허용 하는 다른 규칙을 만듭니다. Azure Portal에 액세스 해야 하는 경우 '*AzurePortal*' 서비스 태그에 대 한 규칙을 추가할 수도 있습니다.

4. VM에 연결 하 고 브라우저를 연 다음 브라우저 콘솔 (Ctrl + Shift + J)로 이동 하 고 네트워크 탭으로 전환 하 여 네트워크 요청을 모니터링 합니다. Url 상자에 web.purview.azure.com를 입력 하 고 AAD 자격 증명을 사용 하 여 로그인을 시도 합니다. 로그인이 실패 했을 수 있으며, 콘솔의 네트워크 탭에서 aadcdn.msauth.net에 액세스 하려고 시도 하는 동안 AAD를 볼 수 있습니다.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="로그인 실패 세부 정보":::

5. 이 경우 VM에서 명령 프롬프트를 열고,이 url (aadcdn.msauth.net)을 ping 하 고, IP를 가져온 다음, VM의 네트워크 보안 규칙에서 IP에 대 한 아웃 바운드 포트 규칙을 추가 합니다. 대상 ip 주소를 설정 하 고 대상 IP 주소를 aadcdn의 IP로 설정 합니다. 부하 분산 장치 및 traffic manager로 인해 AAD CDN의 IP는 동적이 될 수 있습니다. IP를 가져온 후에는 VM의 호스트 파일에 추가 하 여 브라우저에서 해당 IP를 방문 하 여 AAD CDN을 받도록 하는 것이 좋습니다.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="테스트 ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN 규칙":::

6. 새 규칙이 만들어지면 VM으로 다시 이동 하 여 AAD 자격 증명을 사용 하 여 로그인을 다시 시도 합니다. 로그인이 성공 하면 부서의 범위 포털을 사용할 준비가 된 것입니다. 그러나 경우에 따라 AAD는 고객의 계정 유형에 따라 로그인 할 다른 도메인으로 리디렉션됩니다. 예를 들어 live.com 계정의 경우 AAD는 live.com로 리디렉션하여 로그인 합니다. 그러면 해당 요청은 다시 차단 됩니다. Microsoft 직원 계정의 경우 AAD는 로그인 정보에 대 한 msft.sts.microsoft.com에 액세스 합니다. 브라우저 네트워킹 탭의 네트워킹 요청을 확인 하 여 차단 된 도메인의 요청을 확인 하 고, 이전 단계를 다시 실행 하 여 해당 IP에 대 한 요청을 허용 합니다 (가능한 경우 url 및 IP를 VM의 호스트 파일에 추가 하 여 DNS 확인을 수정). 정확한 로그인 도메인의 IP 범위를 알고 있으면 네트워킹 규칙에 직접 추가할 수도 있습니다.

7. 이제 AAD에 로그인 해야 합니다. 부서의 범위 포털은 로드 되지만 모든 부서의 범위 계정은 특정 부서의 범위 계정에만 액세스할 수 있으므로 작동 하지 않습니다. *부서의 범위/resource/{PurviewAccountName}* 를 입력 하 여에 대 한 개인 끝점을 성공적으로 설정 하는 부서의 범위 계정을 직접 방문 합니다.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>기존 부서의 범위 계정에 대해 개인 끝점 사용

부서의 범위 계정을 만든 후에는 부서의 범위 개인 끝점을 추가할 수 있는 두 가지 방법이 있습니다.

- Azure Portal (부서의 범위 계정) 사용
- 개인 링크 센터 사용

### <a name="using-the-azure-portal-purview-account"></a>Azure Portal (부서의 범위 계정) 사용

1. Azure Portal에서 부서의 범위 계정으로 이동 하 고 **설정** 의 **네트워킹** 섹션에서 개인 끝점 연결을 선택 합니다.

:::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="포털 개인 끝점 만들기":::

1. + 개인 끝점을 클릭 하 여 새 개인 끝점을 만듭니다.

1. 기본 정보를 입력 합니다.

1. 리소스 탭에서 리소스 유형을 **부서의 범위/accounts** 로 선택 합니다.

1. 부서의 범위 계정으로 사용할 리소스를 선택 하 고 대상 하위 리소스를 **계정** 으로 선택 합니다.

1. 구성 탭에서 **가상 네트워크** 및 **사설 DNS 영역** 을 선택 합니다. 요약 페이지로 이동 하 고 **만들기** 를 클릭 하 여 포털 개인 끝점을 만듭니다.

> [!NOTE]
> **포털로** 선택 된 대상 하위 리소스에 대해서도 위와 동일한 단계를 수행 해야 합니다.

### <a name="using-the-private-link-center"></a>개인 링크 센터 사용

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 페이지 맨 위에 있는 검색 표시줄에서 ' 개인 링크 '를 검색 하 고 첫 번째 옵션을 클릭 하 여 개인 링크 블레이드로 이동 합니다.

3. ' + 추가 '를 클릭 하 고 기본 세부 정보를 입력 합니다.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="개인 링크 센터에서 PE 만들기":::

4. 이미 만든 부서의 범위 계정으로 사용할 리소스를 선택 하 고 대상 하위 리소스를 **계정** 으로 선택 합니다.

5. 구성 탭에서 가상 네트워크 및 사설 DNS 영역을 선택 합니다. 요약 페이지로 이동 하 고 **만들기** 를 클릭 하 여 계정 개인 끝점을 만듭니다.

> [!NOTE]
> **포털로** 선택 된 대상 하위 리소스에 대해서도 위와 동일한 단계를 수행 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 부서의 범위 Data Catalog 찾아보기](how-to-browse-catalog.md)

- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
