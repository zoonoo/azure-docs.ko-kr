---
title: 위협 인텔리전스 데이터를 Azure Sentinel에 연결| 마이크로 소프트 문서
description: 위협 인텔리전스 데이터를 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: eec07a01edc6b126bb7cd3a814912ea5c5b14195
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529090"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>위협 인텔리전스 제공업체의 데이터 연결

> [!IMPORTANT]
> Azure Sentinel의 위협 인텔리전스 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel을 사용하면 조직에서 사용하는 위협 지표를 가져올 수 있으므로 보안 분석가가 알려진 위협을 탐지하고 우선 순위를 지정할 수 있습니다. 그런 다음 Azure Sentinel의 여러 기능을 사용할 수 있게 되거나 향상된 기능을 제공합니다.

- **애널리틱스에는** 위협 지표의 로그 이벤트 일치를 기반으로 경고 및 인시던트를 생성할 수 있는 예약된 규칙 템플릿 세트가 포함되어 있습니다.

- **통합 문서는** Azure Sentinel로 가져온 위협 지표및 위협 지표와 일치하는 분석 규칙에서 생성된 모든 경고에 대한 요약 정보를 제공합니다.

- **검색** 쿼리를 사용하면 보안 조사관이 일반적인 사냥 시나리오의 컨텍스트 내에서 위협 지표를 사용할 수 있습니다.

- **노트북은** 이상 징후를 조사하고 악의적인 동작을 추적할 때 위협 지표를 사용할 수 있습니다.

다음 섹션에 나열된 통합 위협 인텔리전스 플랫폼(TIP) 제품 중 하나를 사용하거나 TAXII 서버에 연결하거나 [Microsoft 그래프 보안 tiIndicators API와](https://aka.ms/graphsecuritytiindicators)직접 통합하여 위협 지표를 Azure Sentinel로 스트리밍할 수 있습니다.

## <a name="integrated-threat-intelligence-platform-products"></a>통합 위협 인텔리전스 플랫폼 제품

- [MISP 오픈 소스 위협 인텔리전스 플랫폼](https://www.misp-project.org/)
    
    MICROSOFT 그래프 보안 API로 위협 지표를 마이그레이션하는 MISP 인스턴스를 클라이언트에 제공하는 샘플 스크립트는 [MISP에서 Microsoft 그래프 보안 스크립트로](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)이동합니다.

- [아노말리 위협스트림](https://www.anomali.com/products/threatstream)

    ThreatStream 통합자 및 확장 및 ThreatStream 인텔리전스를 Microsoft 그래프 보안 API에 연결하는 방법에 대한 지침을 다운로드하려면 [ThreatStream 다운로드](https://ui.threatstream.com/downloads) 페이지를 참조하십시오.

- [팔로 알토 네트웍스 마인멜드](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    안내 지침은 [MineMeld를 사용하여 Microsoft 그래프 보안 API로 IOC 보내기를](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)참조하십시오.

- [위협 연결 플랫폼](https://threatconnect.com/solution/)

    자세한 내용은 [ThreatConnect 통합을](https://threatconnect.com/integrations/) 참조하고 페이지에서 Microsoft 그래프 보안 API를 찾습니다.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure Sentinel을 위협 인텔리전스 플랫폼에 연결

## <a name="prerequisites"></a>사전 요구 사항  

- 글로벌 관리자 또는 보안 관리자의 Azure AD 역할은 Microsoft 그래프 보안 tiIndicators API와 직접 통합을 사용하는 TIP 제품 또는 사용자 지정 응용 프로그램에 대한 권한을 부여합니다.

- Azure Sentinel 작업 영역에 대한 사용 권한을 읽고 작성하여 위협 표시기를 저장합니다.

## <a name="instructions"></a>Instructions

1. [응용 프로그램](/graph/auth-v2-service#1-register-your-app) ID, 응용 프로그램 보안 및 Azure Active Directory 테넌트 ID를 받으려면 Azure Active Directory에 응용 프로그램을 등록합니다. Microsoft 그래프 보안 tiIndicators API와 직접 통합을 사용하는 통합 TIP 제품 또는 앱을 구성할 때 이러한 값이 필요합니다.

2. 등록된 응용 프로그램에 대한 [API 권한 구성:](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) 등록된 응용 프로그램에 Microsoft 그래프 응용 프로그램 권한 **ThreatIndicators.ReadWrite.OwnedBy를** 추가합니다.

3. Azure Active Directory 테넌트 관리자에게 조직에 등록된 응용 프로그램에 관리자 동의를 부여하도록 요청합니다. Azure 포털에서: **Azure Active Directory** > **App 등록** > **\<_앱 이름_>** > **보기 API 권한** > ** \< _테넌트 이름에_>대 한 관리자 동의 부여**합니다.

4. 다음을 지정하여 Azure Sentinel에 지표를 보내도록 Microsoft 그래프 보안 tiIndicators API와 직접 통합하는 TIP 제품 또는 앱을 구성합니다.
    
    a. 등록된 응용 프로그램의 ID, 비밀 및 테넌트 ID의 값입니다.
    
    b. 대상 제품에 대해 Azure Sentinel을 지정합니다.
    
    다. 작업에 대해 경고를 지정합니다.

5. Azure 포털에서 Azure **Sentinel** > 데이터 커넥터로 이동한 다음 **위협 인텔리전스 플랫폼(미리 보기)** 커넥터를**선택합니다.**

6. **연결 선 열기 페이지를**선택한 다음 **연결**합니다.

7. Azure Sentinel로 가져온 위협 지표를 보려면 **Azure Sentinel - 로그** > **보안Insights로**이동한 다음 **ThreatIntelligenceIndicator**를 확장합니다.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Azure 센티넬을 TAXII 서버에 연결

## <a name="prerequisites"></a>사전 요구 사항  

- Azure Sentinel 작업 영역에 대한 사용 권한을 읽고 작성하여 위협 표시기를 저장합니다.

- TAXII 2.0 서버 URI 및 수집 ID.

## <a name="instructions"></a>Instructions

1. Azure 포털에서 Azure **Sentinel** > **데이터 커넥터로** 이동한 다음 **위협 인텔리전스 - TAXII(미리 보기)** 커넥터를 선택합니다.

2. **연결선 열기 페이지를**선택합니다.

3. 텍스트 상자에 필요한 정보와 선택적 정보를 지정합니다.

4. TAXII 2.0 서버에 연결하려면 **추가를** 선택합니다.

5. 추가 택시 2.0 서버가 있는 경우: 3단계와 4단계를 반복합니다.

6. Azure Sentinel로 가져온 위협 지표를 보려면 **Azure Sentinel - 로그** > **보안Insights로**이동한 다음 **ThreatIntelligenceIndicator**를 확장합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 위협 인텔리전스 공급자를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
