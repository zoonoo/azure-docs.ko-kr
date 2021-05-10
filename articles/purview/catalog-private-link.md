---
title: Purview에 대한 보안 액세스를 위해 프라이빗 엔드포인트 사용
description: 이 문서에서는 Purview 계정에 대한 프라이빗 엔드포인트를 설정하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 09fa10e7f7751321601c5c4871b2cf36ccf6f01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720900"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Purview 계정에 대한 프라이빗 엔드포인트 사용

Purview 계정에 대한 프라이빗 엔드포인트를 사용하여 VNet(가상 네트워크)의 클라이언트 및 사용자가 프라이빗 링크를 통해 카탈로그에 안전하게 액세스할 수 있도록 할 수 있습니다. 프라이빗 엔드포인트는 Purview 계정에 대해 VNet 주소 공간의 IP 주소를 사용합니다. VNet에 있는 사용자와 Purview 계정 사이의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 프라이빗 링크를 가로지르며 공용 인터넷 노출을 방지합니다.

## <a name="create-purview-account-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하여 Purview 계정 만들기

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, Purview 계정으로 이동합니다.

1. 기본 정보를 채우고 **네트워킹** 탭에서 연결 방법을 프라이빗 엔드포인트로 설정합니다. 프라이빗 엔드포인트와 페어링하려는 **구독, VNet 및 서브넷** 에 대한 세부 정보를 제공하여 수집 프라이빗 엔드포인트를 설정합니다.

    > [!NOTE]
    > Azure 및 온-프레미스 원본 모두에 대해 엔드투엔드 검색 시나리오에서 네트워크 격리를 사용하도록 설정하려는 경우에만 수집 프라이빗 엔드포인트를 만듭니다. 현재 AWS 원본에서 작동하는 수집 프라이빗 엔드포인트를 지원하지 않습니다.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Azure Portal을 사용하여 프라이빗 엔드포인트 만들기":::

1. 필요에 따라 각 수집 프라이빗 엔드포인트에 대한 **프라이빗 DNS 영역** 을 설정하도록 선택할 수도 있습니다.

1. 추가를 클릭하여 Purview 계정에 대한 프라이빗 엔드포인트를 추가합니다.

1. 프라이빗 엔드포인트 만들기 페이지에서 Purview 하위 리소스를 **계정** 으로 설정하고, 가상 네트워크 및 서브넷을 선택하고, DNS를 등록할 프라이빗 DNS 영역을 선택합니다(또한 사용자 고유의 DNS 서버를 활용하거나 가상 머신의 호스트 파일을 사용하여 DNS 레코드를 만들 수도 있음).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="프라이빗 엔드포인트 만들기 선택 항목":::

1. **확인** 을 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="계정을 만들기 위한 유효성 검사를 통과함":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Purview 웹 포털에 대한 프라이빗 엔드포인트 만들기

1. 방금 만든 Purview 계정으로 이동하고, 설정 섹션에서 프라이빗 엔드포인트 연결을 선택합니다.

1. +프라이빗 엔드포인트를 클릭하여 새 프라이빗 엔드포인트를 만듭니다.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="포털 프라이빗 엔드포인트 만들기":::

1. 기본 정보를 채웁니다.

1. 리소스 탭에서 리소스 종류를 **Microsoft.Purview/accounts** 로 선택합니다.

1. 해당 리소스를 새로 만든 Purview 계정으로 선택하고 대상 하위 리소스를 **포털** 로 선택합니다.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="포털 프라이빗 엔드포인트에 대한 세부 정보":::

1. 구성 탭에서 가상 네트워크 및 프라이빗 DNS 영역을 선택합니다. 요약 페이지로 이동하고 **만들기** 를 클릭하여 포털 프라이빗 엔드포인트를 만듭니다.

## <a name="enabling-access-to-azure-active-directory"></a>Azure Active Directory에 대한 액세스 사용

> [!NOTE]
> VM, VPN Gateway 또는 VNET 피어링 게이트웨이에서 퍼블릭 인터넷에 액세스할 수 있는 경우 프라이빗 엔드포인트에서 사용하도록 설정된 Purview 포털 및 Purview 계정에 액세스할 수 있으며 아래 지침의 나머지 부분을 따를 필요가 없습니다. 프라이빗 네트워크의 네트워크 보안 그룹 규칙이 모든 퍼블릭 인터넷 트래픽을 거부하도록 설정된 경우 AAD 액세스를 사용하도록 설정하는 몇 가지 규칙을 추가해야 합니다. 이렇게 하려면 아래 지침을 따르세요.

아래 지침은 Azure VM에서 Purview에 안전하게 액세스하기 위한 것입니다. VPN 또는 다른 VNet 피어링 게이트웨이를 사용하는 경우에도 유사한 단계를 수행해야 합니다.

1. Azure Portal에서 VM으로 이동하고 설정 섹션에서 네트워킹 탭을 선택합니다. 아웃바운드 포트 규칙을 선택하고 아웃바운드 포트 규칙 추가를 클릭합니다.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="아웃바운드 규칙 추가":::

2. 아웃바운드 포트 규칙 추가 블레이드에서 *대상* 을 서비스 태그로 선택하고, 대상 서비스 태그를 **AzureActiveDirectory** 로 선택하고, 대상 포트 범위를 *로 지정하고, 작업을 허용으로 선택하고, **우선 순위는 모든 인터넷 트래픽을 거부한 규칙보다 높아야 합니다**. 규칙을 만듭니다.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="아웃바운드 규칙 세부 정보 추가":::

3. 동일한 단계에 따라 '**AzureResourceManager**' 서비스 태그를 허용하는 다른 규칙을 만듭니다. Azure Portal에 액세스해야 하는 경우 'AzurePortal' 서비스 태그에 대한 규칙을 추가할 수도 있습니다.

4. VM에 연결하고 브라우저를 연 다음, 브라우저 콘솔로 이동하고(Ctrl+Shift+J), 네트워크 탭으로 전환하여 네트워크 요청을 모니터링합니다. URL 상자에 web.purview.azure.com을 입력하고 AAD 자격 증명을 사용하여 로그인을 시도합니다. 로그인이 실패했을 수 있으며, 콘솔의 네트워크 탭에서 AAD가 aadcdn.msauth.net에 액세스하려고 시도하지만 차단된 것을 확인할 수 있습니다.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="로그인 실패 세부 정보":::

5. 이 경우 VM에서 명령 프롬프트를 열고, 이 URL(aadcdn.msauth.net)을 ping하고, IP를 가져온 다음, VM의 네트워크 보안 규칙에서 IP에 대한 아웃바운드 포트 규칙을 추가합니다. 대상을 IP 주소를 설정하고 대상 IP 주소를 aadcdn의 IP로 설정합니다. 부하 분산 장치 및 Traffic Manager로 인해 AAD CDN의 IP는 동적이 될 수 있습니다. IP를 가져온 후에는 VM의 호스트 파일에 추가하여 강제로 브라우저에서 해당 IP를 방문하여 AAD CDN을 얻도록 하는 것이 좋습니다.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="테스트 ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN 규칙":::

6. 새 규칙이 만들어지면 VM으로 다시 이동하여 AAD 자격 증명을 사용하여 다시 로그인해 봅니다. 로그인이 성공하면 Purview 포털을 사용할 준비가 된 것입니다. 그러나 경우에 따라 AAD는 고객의 계정 유형에 따라 로그인할 다른 도메인으로 리디렉션됩니다. 예를 들어, live.com 계정의 경우 AAD는 live.com로 리디렉션하여 로그인합니다. 그러면 해당 요청은 다시 차단됩니다. Microsoft 직원 계정의 경우 AAD는 msft.sts.microsoft.com에 액세스하여 로그인 정보를 얻을 수 있습니다. 브라우저 네트워킹 탭의 네트워킹 요청을 확인하여 차단된 도메인의 요청을 확인하고, 이전 단계를 다시 실행하여 해당 IP를 가져오고 네트워크 보안 그룹에 아웃바운드 포트 규칙을 추가하여 해당 IP에 대한 요청을 허용합니다(가능한 경우 URL 및 IP를 VM의 호스트 파일에 추가하여 DNS 확인을 수정함). 정확한 로그인 도메인의 IP 범위를 알고 있으면 네트워킹 규칙에 직접 추가할 수도 있습니다.

7. 이제 AAD에 성공적으로 로그인할 수 있습니다. Purview Portal은 로드되지만 특정 Purview 계정에만 액세스할 수 있으므로 모든 Purview 계정이 나열되지는 않습니다. *web.purview.azure.com/resource/{PurviewAccountName}* 을(를) 입력하여 프라이빗 엔드포인트를 성공적으로 설정한 Purview 계정을 직접 방문합니다.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>프라이빗 네트워크, VNet에서, 프라이빗 엔드포인트 뒤의 수집 프라이빗 엔드포인트 및 검색 원본

검색되는 원본에서 Purview DataMap으로 흐르는 메타데이터에 대한 네트워크 격리를 보장하려면 다음 단계를 수행해야 합니다.
1. [이](#creating-an-ingestion-private-endpoint) 섹션의 단계를 수행하여 **수집 프라이빗 엔드포인트** 를 사용하도록 설정합니다.
1. **자체 호스팅 IR** 을 사용하여 원본을 검색합니다.
 
    1. SQL Server, Oracle, SAP 등과 같은 모든 온-프레미스 원본 유형은 현재 자체 호스팅 IR 기반 검사를 통해서만 지원됩니다. 자체 호스팅 IR은 프라이빗 네트워크 내에서 실행된 다음, Azure에서 VNet과 피어링되어야 합니다. 그런 다음, [아래](#creating-an-ingestion-private-endpoint) 단계를 수행하여 수집 프라이빗 엔드포인트에서 Azure VNet을 사용하도록 설정해야 합니다. 
    1. Azure Blob Storage, Azure SQL Database 등과 같은 모든 **Azure** 원본 유형의 경우, 자체 호스팅 IR을 사용하여 검색하도록 명시적으로 선택하여 네트워크 격리를 보장해야 합니다. 자체 호스팅 IR을 설정하려면 [여기](manage-integration-runtimes.md)의 단계를 따르세요. 그런 다음, **Integration Runtime을 통해 연결** 드롭다운에서 자체 호스트 IR을 선택하여 네트워크 격리를 보장하도록 Azure 원본에 대해 검색을 설정합니다. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="자체 호스팅 IR을 사용하여 Azure 검색 실행":::

> [!NOTE]
> 현재는 자체 호스팅 IR을 사용하여 Azure 원본을 검색할 때 MSI 자격 증명 방법을 지원하지 않습니다. 해당 Azure 원본에 대해 지원되는 다른 자격 증명 방법 중 하나를 사용해야 합니다.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>기존 Purview 계정에 대해 프라이빗 엔드포인트 사용

Purview 계정을 만든 후에는 다음 두 가지 방법으로 Purview 프라이빗 엔드포인트를 추가할 수 있습니다.

- Azure Portal(Purview 계정) 사용
- 프라이빗 링크 센터 사용

### <a name="using-the-azure-portal-purview-account"></a>Azure Portal(Purview 계정) 사용

1. Azure Portal에서 Purview 계정으로 이동하고 **설정** 의 **네트워킹** 섹션에서 프라이빗 엔드포인트 연결을 선택합니다.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="계정 프라이빗 엔드포인트 만들기":::

1. +프라이빗 엔드포인트를 클릭하여 새 프라이빗 엔드포인트를 만듭니다.

1. 기본 정보를 채웁니다.

1. 리소스 탭에서 리소스 종류를 **Microsoft.Purview/accounts** 로 선택합니다.

1. 해당 리소스를 Purview 계정으로 선택하고 대상 하위 리소스를 **계정** 으로 선택합니다.

1. 구성 탭에서 **가상 네트워크** 및 **프라이빗 DNS 영역** 을 선택합니다. 요약 페이지로 이동하고 **만들기** 를 클릭하여 포털 프라이빗 엔드포인트를 만듭니다.

> [!NOTE]
> **포털** 로 선택된 대상 하위 리소스에 대해서도 위와 동일한 단계를 수행해야 합니다.

#### <a name="creating-an-ingestion-private-endpoint"></a>수집 프라이빗 엔드포인트 만들기

1. Azure Portal에서 Purview 계정으로 이동하고 **설정** 의 **네트워킹** 섹션에서 프라이빗 엔드포인트 연결을 선택합니다.
1. **수집 프라이빗 엔드포인트 연결** 탭으로 이동하고 **+새로 만들기** 를 클릭하여 새 수집 프라이빗 엔드포인트를 만듭니다.

1. 기본 정보 및 VNet 세부 정보를 입력합니다.
 
    :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="프라이빗 엔드포인트 세부 정보 입력":::

1. **만들기** 를 클릭하여 설정을 마칩니다.

> [!NOTE]
> 수집 프라이빗 엔드포인트는 위에서 설명한 Purview Azure Portal 환경을 통해서만 만들 수 있습니다. 프라이빗 링크 센터에서는 만들 수 없습니다.

### <a name="using-the-private-link-center"></a>프라이빗 링크 센터 사용

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 페이지 맨 위에 있는 검색 표시줄에서 '프라이빗 링크'를 검색하고 첫 번째 옵션을 클릭하여 프라이빗 링크 블레이드로 이동합니다.

3. '+추가'를 클릭하고 기본 세부 정보를 입력합니다.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="프라이빗 링크 센터에서 PE 만들기":::

4. 해당 리소스를 이미 만들어진 Purview 계정으로 선택하고 대상 하위 리소스를 **계정** 으로 선택합니다.

5. 구성 탭에서 가상 네트워크 및 프라이빗 DNS 영역을 선택합니다. 요약 페이지로 이동하고 **만들기** 를 클릭하여 계정 프라이빗 엔드포인트를 만듭니다.

> [!NOTE]
> **포털** 로 선택된 대상 하위 리소스에 대해서도 위와 동일한 단계를 수행해야 합니다.

## <a name="firewalls-to-restrict-public-access"></a>퍼블릭 액세스를 제한하는 방화벽

퍼블릭 인터넷에서 Purview 계정에 대한 액세스를 완전히 차단하려면 아래 단계를 수행합니다. 이 설정은 프라이빗 엔드포인트 및 수집 프라이빗 엔드포인트 연결 둘 다에 적용됩니다.

1. Azure Portal에서 Purview 계정으로 이동하고 **설정** 의 **네트워킹** 섹션에서 프라이빗 엔드포인트 연결을 선택합니다.
1. 방화벽 탭으로 이동하여 토글이 **거부** 로 설정되어 있는지 확인합니다.

    :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="프라이빗 엔드포인트 방화벽 설정":::

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)

- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
