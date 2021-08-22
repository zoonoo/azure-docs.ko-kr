---
title: Azure WAF(Web Application Firewall) 데이터를 Azure Sentinel에 연결
description: Azure WAF 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: cd1ec5d11083c21511b39ba08fa2bfcf159bfc54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529361"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Azure WAF(Web Application Firewall)에서 데이터 연결

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Azure WAF(Web Application Firewall)는 코드 삽입 및 교차 사이트 스크립팅과 같은 일반적인 악용 및 위협으로부터 웹 애플리케이션에 대한 중앙화된 보호를 제공합니다. Azure WAF는 [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) 서비스, [Azure Front Door](../web-application-firewall/afds/afds-overview.md) 서비스 및 [Azure CDN(Content Delivery Network)](../web-application-firewall/cdn/cdn-overview.md) WAF 정책(현재 공개 미리 보기로 제공)을 통해 배포할 수 있습니다.
Azure WAF 로그를 Azure Sentinel에 연결하여 통합 문서에서 로그 데이터를 확인하고 이를 사용하여 사용자 지정 경고를 만든 다음 이를 통합하여 조사를 개선할 수 있습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 사용 권한이 있어야 합니다.

## <a name="connect-to-azure-waf"></a>Azure WAF에 연결

### <a name="instructions-tab"></a>지침 탭

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure WAF(Web Application Firewall)** 를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 연결할 로그가 포함된 WAF 리소스의 유형에 대한 링크 선택 - **Application Gateway 리소스 열기>** , **Front Door 리소스 열기 >** , 또는 **CDN(Content Delivery Network) WAF 정책 열기 >** - 리소스 목록의 화면에서 표시되면 목록에서 WAF 리소스를 선택합니다.

    1. WAF 리소스의 탐색 메뉴에서 **진단 설정** 을 선택합니다.

    1. 목록의 맨 아래에 있는 **+ 진단 설정 추가** 를 선택합니다.

    1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력합니다.

    1. **Log Analytics에 보내기** 확인란을 선택합니다. 두 개의 새 필드가 아래에 표시됩니다. 관련 **구독** 및 **Log Analytics 작업 영역**(Azure Sentinel이 있는 위치)을 선택합니다.

    1. 수집하려는 로그의 규칙 유형에 대한 확인란을 클릭합니다. 각 리소스 종류에 대한 권장 사항은 다음과 같습니다.

        | 리소스 | 수집을 위해 선택할 로그 |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF 정책      | WebApplicationFirewallLogs |
        |

    1. **저장** 을 선택합니다.

### <a name="next-steps-tab"></a>다음 단계 탭

- Azure WAF 로그 데이터에 대한 정보를 얻으려면 **Azure Web Application Firewall** 데이터 커넥터와 함께 제공되는 권장 통합 문서, 쿼리 샘플 및 분석 규칙 템플릿을 참조하세요.

- **로그** 에서 Azure WAF 데이터를 쿼리하려면 쿼리 창에서 **AzureDiagnostics** 를 입력합니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 지난 2주 동안 어느 지점에서 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색상 줄무늬 및 데이터 형식 이름 옆의 연결 아이콘)가 *연결됨*(녹색)으로 표시됩니다. 2주 동안 데이터 수집이 없으면 커넥터는 연결 끊김으로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure WAF 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.