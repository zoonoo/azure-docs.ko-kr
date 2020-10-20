---
title: '빠른 시작: Azure Sentinel에서 온보딩'
description: 이 빠른 시작에서는 먼저 Sentinel을 활성화한 다음, 데이터 원본을 연결하여 Azure Sentinel을 온보딩하는 방법을 알아봅니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: da1f3154d492a36a196d87eec98af462fd659cd2
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057521"
---
# <a name="quickstart-on-board-azure-sentinel"></a>빠른 시작: Azure Sentinel 온보딩

이 빠른 시작에서는 Azure Sentinel을 온보딩하는 방법을 알아봅니다. 

Azure Sentinel을 온보딩하려면 먼저 Azure Sentinel을 사용하도록 설정한 후 데이터 원본을 연결해야 합니다. Azure Sentinel에는 Microsoft 365 Defender(이전에는 Microsoft Threat Protection) 솔루션, Microsoft 365 원본(Office 365 포함), Azure AD, Microsoft Defender for Identity(이전에는 Azure ATP), Microsoft Cloud App Security, Azure Security Center의 Azure Defender 경고를 포함하여 즉시 사용 가능하고 실시간 통합을 제공하는 다양한 Microsoft 솔루션용 커넥터가 포함되어 있습니다. 또한 타사 솔루션에 대한 광범위한 보안 에코시스템에 기본 제공 커넥터도 제공됩니다. CEF(Common Event Format), Syslog 또는 REST API를 사용하여 Azure Sentinel에 데이터 원본을 연결할 수도 있습니다. 

데이터 원본을 연결한 후 데이터에 따른 인사이트를 제공하는 전문가가 만든 통합 문서 갤러리 중에서 선택합니다. 이러한 통합 문서는 사용자의 요구에 맞게 쉽게 사용자 지정할 수 있습니다.

>[!IMPORTANT] 
> Azure Sentinel 사용 시 발생하는 요금에 대한 자세한 내용은 [Azure Sentinel 가격 책정](https://azure.microsoft.com/pricing/details/azure-sentinel/)을 참조하세요.

## <a name="global-prerequisites"></a>전역 전제 조건

- 활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Log Analytics 작업 영역. [Log Analytics 작업 영역을 만드는 방법](../log-analytics/log-analytics-quick-create-workspace.md)을 알아봅니다. Log Analytics 작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../azure-monitor/platform/design-logs-deployment.md)을 참조하세요.

- Azure Sentinel을 사용하도록 설정하려면 Azure Sentinel 작업 영역이 있는 구독에 대한 참가자 권한이 필요합니다. 
- Azure Sentinel을 사용하려면 작업 영역이 속한 리소스 그룹에 대해 참가자 또는 읽기 권한자 권한이 있어야 합니다.
- 특정 데이터 원본을 연결하려면 추가 권한이 필요할 수 있습니다.
- Azure Sentinel은 유료 서비스입니다. 가격 책정 정보에 대해서는 [Azure Sentinel 정보](https://go.microsoft.com/fwlink/?linkid=2104058)를 참조하세요.

### <a name="geographical-availability-and-data-residency"></a>지리적 가용성 및 데이터 상주

- Azure Sentinel은 중국과 독일(소버린) 지역을 제외한 모든 [Log Analytics의 GA 지역](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)의 작업 영역에서 실행할 수 있습니다. 

- 인시던트, 책갈피 및 분석 규칙과 같이 Azure Sentinel에 의해 생성된 데이터에는 고객의 Log Analytics 작업 영역에서 소싱된 일부 고객 데이터가 포함될 수 있습니다. 이 Azure Sentinel 생성 데이터는 작업 영역이 있는 지리에 따라 다음 표에 나열된 지리에 저장됩니다.

    | 작업 영역 지리 | Azure Sentinel 생성 데이터 지리 |
    | --- | --- |
    | 미국<br>인도<br>브라질<br>아프리카<br>한국 | 미국 |
    | 유럽<br>프랑스<br>스위스 | 유럽 |
    | 오스트레일리아 | 오스트레일리아 |
    | United Kingdom | United Kingdom |
    | Canada | Canada |
    | 아시아 태평양 | 아시아 태평양(싱가포르) |
    | 일본 | 일본 |
    |

## <a name="enable-azure-sentinel"></a>Azure Sentinel 사용<a name="enable"></a>

1. Azure Portal에 로그인합니다. Azure Sentinel이 생성된 구독이 선택되었는지 확인합니다.

1. **Azure Sentinel**을 검색하여 선택합니다.

   ![서비스 검색](./media/quickstart-onboard/search-product.png)

1. **추가**를 선택합니다.

1. 사용하려는 작업 영역을 선택하거나 새 작업 영역을 만듭니다. Azure Sentinel은 둘 이상의 작업 영역에서 실행할 수 있지만 데이터는 단일 작업 영역으로 격리됩니다.

   ![작업 영역 선택](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure Security Center에서 만든 기본 작업 영역은 목록에 표시되지 않습니다. 기본 작업 영역에는 Azure Sentinel을 설치할 수 없습니다.
   >

   >[!IMPORTANT]
   >
   > - 작업 영역에 배포되면 Azure Sentinel은 해당 작업 영역을 다른 리소스 그룹 또는 구독으로 이동하는 것을 **현재 지원하지 않습니다**. 
   >
   >   작업 영역을 이미 이동한 경우 **Analytics**에서 모든 활성 규칙을 사용하지 않도록 설정하고 5분 후에 다시 사용하도록 설정합니다. 대부분의 경우에 이를 효과적으로 수행해야 하지만, 이는 지원되지 않으며 사용자의 책임하에 수행됩니다.

1. **Azure Sentinel 추가**를 선택합니다.

## <a name="connect-data-sources"></a>데이터 원본 연결

Azure Sentinel은 서비스에 연결하고 이벤트 및 로그를 Azure Sentinel에 전달하여 서비스 및 앱에서 데이터를 수집합니다. 실제 및 가상 머신의 경우 로그를 수집하고 Azure Sentinel에 전달하는 Log Analytics 에이전트를 설치할 수 있습니다. 방화벽 및 프록시의 경우 Azure Sentinel은 Linux Syslog 서버에 Log Analytics 에이전트를 설치합니다. 여기서 에이전트는 로그 파일을 수집하여 Azure Sentinel에 전달합니다. 
 
1. 주 메뉴에서 **데이터 커넥터**를 선택합니다. 그러면 데이터 커넥터 갤러리가 열립니다.

1. 갤러리는 연결할 수 있는 모든 데이터 원본의 목록입니다. 데이터 원본을 선택한 다음, **커넥터 페이지 열기** 단추를 선택합니다.

1. 커넥터 페이지에는 커넥터 구성 지침과 필요한 추가 지침이 표시됩니다.<br>
예를 들어 Azure AD에서 Azure Sentinel로 로그를 스트리밍할 수 있는 **Azure Active Directory** 데이터 원본을 선택하는 경우 가져올 로그 유형(로그인 로그 및/또는 감사 로그)을 선택할 수 있습니다. <br> 자세한 내용은 설치 지침을 따르거나 [관련 연결 가이드](connect-data-sources.md)를 참조하세요. 데이터 커넥터에 대한 내용은 [Microsoft 서비스 연결](connect-data-sources.md)을 참조하세요.

1. 커넥터 페이지의 **다음 단계** 탭에는 데이터 커넥터와 함께 제공되는 관련 기본 제공 통합 문서, 샘플 쿼리 및 분석 규칙 템플릿이 표시됩니다. 이를 그대로 사용하거나 수정할 수 있습니다. 두 가지 방법 모두 데이터에 대한 흥미로운 인사이트를 즉시 가져올 수 있습니다. <br>

데이터 원본이 연결된 후 데이터는 Azure Sentinel로 스트리밍되기 시작하고 작업을 시작할 준비가 됩니다. [기본 제공 통합 문서](quickstart-get-visibility.md)에서 로그를 확인하고 Log Analytics에서 쿼리를 작성하여 [데이터를 조사](tutorial-investigate-cases.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 데이터 원본을 온보딩 및 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [일반적인 이벤트 형식 어플라이언스](connect-common-event-format.md)의 데이터를 Azure Sentinel로 스트리밍합니다.
