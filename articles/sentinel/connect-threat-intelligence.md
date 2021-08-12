---
title: Azure Sentinel에 위협 인텔리전스 데이터 연결| Microsoft Docs
description: Azure Sentinel에 위협 인텔리전스 데이터를 연결하는 방법에 관해 알아봅니다.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96121741"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>위협 인텔리전스 공급자의 데이터 연결

> [!IMPORTANT]
> Azure Sentinel의 위협 인텔리전스 데이터 커넥터는 현재 퍼블릭 미리 보기 상태입니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel을 사용하면 조직이 사용 중인 위협 지표를 가져올 수 있으므로 보안 분석가는 알려진 위협을 탐지하고 우선 순위를 지정하는 능력을 향상할 수 있습니다. Azure Sentinel의 여러 기능을 사용할 수 있게 되거나 다음이 향상됩니다.

- **분석** 에는 위협 지표에서 로그 이벤트의 일치를 기반으로 경고 및 인시던트를 생성하는 데 사용할 수 있는 예약된 규칙 템플릿 세트가 포함됩니다.

- **통합 문서** 는 Azure Sentinel로 가져온 위협 지표 및 위협 지표와 일치하는 분석 규칙에서 생성된 모든 경고에 대한 요약 정보를 제공합니다.

- **헌팅** 쿼리를 통해 보안 조사자는 일반적인 헌팅 시나리오의 컨텍스트 내에서 위협 지표를 사용할 수 있습니다.

- **Notebooks** 는 변칙을 조사하고 악의적인 동작을 헌팅하는 경우 위협 지표를 사용할 수 있습니다.

다음 섹션에서 통합된 TIP(위협 인텔리전스 플랫폼) 제품 중 하나를 사용하거나, TAXII 서버에 연결하거나, [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator)와 직접 통합하여 위협 지표를 Azure Sentinel로 스트림할 수 있습니다.

## <a name="integrated-threat-intelligence-platform-products"></a>통합된 위협 인텔리전스 플랫폼 제품

- [MISP 오픈 소스 위협 인텔리전스 플랫폼](https://www.misp-project.org/)
    
    클라이언트에 MISP 인스턴스를 제공하여 위협 지표를 Microsoft Graph 보안 API로 마이그레이션하는 샘플 스크립트는 [Microsoft Graph 보안 스크립트에 대한 MISP](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)를 참조하세요.

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    ThreatStream Integrator 및 확장을 다운로드하고 ThreatStream 인텔리전스를 Microsoft Graph 보안 API에 연결하기 위한 지침을 확인하려면 [ThreatStream 다운로드](https://ui.threatstream.com/downloads) 페이지를 참조하세요.

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    단계별 지침은 [MineMeld를 사용하여 Microsoft Graph 보안 API로 IOC 전송](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)을 참조하세요.

- [ThreatConnect 플랫폼](https://threatconnect.com/solution/)

    정보는 [ThreatConnect 통합](https://threatconnect.com/integrations/)을 참조하고 페이지에서 Microsoft Graph 보안 API를 찾아보세요.

- [EclecticIQ 플랫폼](https://www.eclecticiq.com/solutions)

- [ThreatQ 위협 인텔리전스 플랫폼](https://www.threatq.com/)

    정보와 단계별 지침은 [Microsoft Sentinel Connector For ThreatQ 통합](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview)을 참조하세요.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure Sentinel을 위협 인텔리전스 플랫폼에 연결

### <a name="prerequisites"></a>필수 구성 요소  

- Microsoft Graph Security tiIndicators API에 대한 직접 통합을 사용하는 TIP 제품이나 사용자 지정 애플리케이션에 대한 권한을 부여할 수 있는 전역 관리자 또는 보안 관리자의 Azure AD 역할.

- 위협 지표를 저장할 수 있는 Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한.

### <a name="instructions"></a>지침

1. 애플리케이션 ID, 애플리케이션 비밀, Azure Active Directory 테넌트 ID를 가져오려면 Azure Active Directory에 [애플리케이션을 등록](/graph/auth-v2-service#1-register-your-app)합니다. Microsoft Graph Security tiIndicators API에 대한 직접 통합을 사용하는 통합된 TIP 제품이나 앱을 구성하는 경우 이 값이 필요합니다.

2. 등록된 애플리케이션에 대한 [API 권한 구성](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph): 등록된 애플리케이션에 Microsoft Graph Application 권한인 **ThreatIndicators.ReadWrite.OwnedBy** 를 추가합니다.

3. Azure Active Directory 테넌트 관리자에게 문의하여 조직의 등록된 애플리케이션에 관리자 동의를 허용하도록 요청합니다. Azure Portal에서: **Azure Active Directory** > **앱 등록** >  **\<_app name_>**  > **API 권한 보기** >  **\<_tenant name_>에 대한 관리자 동의 허용**.

4. 다음을 지정하여 Azure Sentinel에 지표를 보내도록 Microsoft Graph Security tiIndicators API에 대한 직접 통합을 사용하는 TIP 제품이나 앱을 구성합니다.
    
    a. 등록된 애플리케이션의 ID, 비밀, 테넌트 ID의 값.
    
    b. 대상 제품의 경우 Azure Sentinel을 지정합니다.
    
    다. 작업의 경우 경고를 지정합니다.

5. Azure Portal에서 **Azure Sentinel** > **데이터 커넥터** 로 이동한 다음, **위협 인텔리전스 플랫폼(미리 보기)** 커넥터를 선택합니다.

6. **커넥터 페이지 열기** 를 선택한 다음, **연결** 을 선택합니다.

7. Azure Sentinel로 가져온 위협 지표를 보려면 **Azure Sentinel-Logs** > **SecurityInsights** 로 이동한 다음, **ThreatIntelligenceIndicator** 를 확장합니다.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>TAXII 서버에 Azure Sentinel 연결

### <a name="prerequisites"></a>필수 구성 요소

- 위협 지표를 저장할 수 있는 Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한.

- TAXII 2.0 서버 URI 및 컬렉션 ID.

### <a name="instructions"></a>지침

1. Azure Portal에서 **Azure Sentinel** > **데이터 커넥터** 로 이동한 다음, **위협 인텔리전스 - TAXII(미리 보기)** 커넥터를 선택합니다.

2. **커넥터 페이지 열기** 를 선택합니다.

3. 텍스트 상자에 필수 및 선택적 정보를 지정합니다.

4. **추가** 를 선택하여 TAXII 2.0 서버에 대한 연결을 사용하도록 설정합니다.

5. 추가 TAXII 2.0 서버가 있는 경우: 3단계와 4단계를 반복합니다.

6. Azure Sentinel로 가져온 위협 지표를 보려면 **Azure Sentinel-Logs** > **SecurityInsights** 로 이동한 다음, **ThreatIntelligenceIndicator** 를 확장합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 위협 인텔리전스 공급자를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 관한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./tutorial-detect-threats-built-in.md)을 시작합니다.