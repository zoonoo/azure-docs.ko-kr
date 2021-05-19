---
title: Azure Portal을 사용하여 Azure Event Grid 파트너로 온보딩
description: Azure Portal을 사용하여 Azure Event Grid 파트너를 온보딩합니다.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050955"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Azure Portal를 사용하여 Azure Event Grid 파트너로 온보딩
이 문서에서는 타사 SaaS 공급자(이벤트 게시자 또는 파트너라고도 함)가 해당 서비스에서 이벤트를 게시할 수 있도록 Event Grid에 온보딩되는 방식과 이러한 이벤트가 최종 사용자에 의해 사용되는 방식에 대해 설명합니다.

> [!IMPORTANT]
> 파트너 이벤트에 익숙하지 않은 경우 이 도움말의 단계를 이해하고 따르는 데 중요한 핵심 개념에 대한 자세한 소개를 보려면 [파트너 이벤트 개요](partner-events-overview.md)를 참조하세요.

## <a name="onboarding-process-for-event-publishers-partners"></a>이벤트 게시자(파트너)를 위한 온보딩 프로세스
사용자가 서비스 이벤트를 사용할 수 있도록 하려면 일반적으로 다음 프로세스가 필요합니다.

1. 다음 단계를 진행하기 전에 Event Grid 서비스 팀에 파트너가 되고자 한다고 **알립니다**.
1. **등록 정보** 를 작성하여 파트너 토픽 유형을 만듭니다. 
1. **네임스페이스** 를 만듭니다.
1. **이벤트 채널** 및 **파트너 토픽** 을 만듭니다(단일 단계).
1. 엔드투엔드 파트너 이벤트 기능을 테스트합니다.

#4단계에서는 제공하려는 사용자 환경의 종류를 결정해야 합니다. 다음과 같은 옵션이 있습니다.
- SDK 및/또는 REST API를 사용하여 도메인에 호스트되는 웹 GUI(그래픽 사용자 인터페이스) 환경에서 사용자 고유의 솔루션을 제공하여 이벤트 채널과 해당하는 파트너 토픽을 만듭니다. 이 옵션을 사용하여 파트너 토픽을 만들 구독 및 리소스 그룹을 사용자에게 요청할 수 있습니다.
- Azure Portal 또는 CLI를 사용하여 이벤트 채널과 연결된 파트너 토픽을 만듭니다. 이 옵션을 사용하여 Azure 구독의 임의 방법으로 파트너 토픽을 만들 리소스 그룹에 있어야 합니다. 

이 문서에서는 Azure Portal을 사용하여 Azure Event Grid 파트너로 온보딩하는 방법을 보여 줍니다. 

> [!NOTE]
> 파트너 토픽 유형을 등록하는 단계는 선택 사항입니다. 파트너 토픽 유형을 작성해야 하는지 결정하려면 [이벤트 게시자가 관리하는 리소스](partner-events-overview.md#resources-managed-by-event-publishers)를 참조하세요.

## <a name="communicate-your-interest-in-becoming-a-partner"></a>파트너가 되고자 한다고 알립니다.
[이 양식](https://aka.ms/gridpartnerform)을 작성하고 [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com)의 Event Grid 팀에 문의하세요. 파트너 이벤트의 사용 사례, 페르소나, 온보딩 프로세스, 기능, 가격 책정 등에 대한 자세한 정보를 제공하는 대화를 나눌 것입니다.

## <a name="prerequisites"></a>필수 구성 요소
나머지 단계를 완료하려면 다음을 수행해야 합니다.

- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- Azure [테넌트](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="register-a-partner-topic-type-optional"></a>파트너 토픽 유형 등록(선택 사항)
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 창에서 **모든 서비스** 를 선택한 다음 검색 창에  **Event Grid 파트너 등록** 을 입력하고 선택합니다. 
1. **Event Grid 파트너 등록** 페이지의 도구 모음에서 **+추가** 를 선택합니다. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="파트너 등록 링크 추가":::
1. **파트너 토픽 유형 등록 만들기 - 기본** 페이지에서 다음 정보를 입력합니다. 
    1. **프로젝트 세부 정보** 섹션에서 다음 단계를 따르세요.
        1. Azure **구독** 을 선택합니다. 
        1. 기존 Azure **리소스 그룹** 을 선택하거나 새 리소스 그룹을 만듭니다. 
    1. **등록 세부 정보** 섹션에서 다음 단계를 따르세요.
        1. **등록 이름** 에 등록 이름을 입력합니다. 
        1. **조직 이름** 에 조직 이름을 입력합니다. 
    1. **파트너 리소스 유형** 섹션에서 **파트너 토픽 만들기** 페이지에 표시될 리소스 유형에 대한 세부 정보를 입력합니다. 
        1. **파트너 리소스 유형 이름** 에 리소스 유형의 이름을 입력합니다. Azure 구독에서 만들어지는 파트너 토픽의 유형입니다. 
        2. **표시 이름** 에 파트너 토픽(자원) 유형에 대한 사용자에게 친숙한 표시 이름을 입력합니다. 
        3. **리소스 유형에 대한 설명** 을 입력합니다. 
        4. **시나리오에 대한 설명** 을 입력합니다. 리소스에 대한 파트너 토픽을 사용할 수 있는 방법 또는 시나리오를 설명해야 합니다.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="파트너 등록 만들기":::            
1. 페이지 하단에서 **다음: 사용자 지정 서비스** 를 선택합니다. **파트너 등록 만들기** 페이지의 **고객 서비스** 탭에서 구독자 사용자가 이벤트 원본에 문제가 있는 경우 연락처 정보를 입력합니다.
    1. **전화 번호** 를 입력합니다.
    1. 전화 번호의 **내선 번호** 를 입력합니다.
    1. 지원 웹 사이트 **URL** 을 입력합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="파트너 등록 만들기 - 고객 서비스":::        
1. 페이지 하단에서 **다음: 태그** 를 선택합니다. 
1. **태그** 페이지에서 다음 값을 구성합니다. 
    1. 추가할 태그의 **이름** 및 **값** 을 입력합니다. 이 단계는 **선택 사항** 입니다. 
    1. 페이지 하단에서 **검토 + 만들기** 를 선택하여 등록(파트너 토픽 유형)을 만듭니다.

## <a name="create-a-partner-namespace"></a>파트너 네임페이스 만들기

1. Azure Portal의 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택한 다음 검색 창에 **Event Grid 파트너 네임스페이스** 를 입력한 다음 목록에서 선택합니다. 
1. **Event Grid 파트너 네임스페이스** 페이지의 도구 모음에서 **+ 추가** 를 선택합니다. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="파트너 네임스페이스 - 링크 추가":::
1. **파트너 네임스페이스 만들기 - 기본** 페이지에서 다음 정보를 지정합니다.
    1. **프로젝트 세부 정보** 섹션에서 다음 단계를 수행합니다. 
        1. Azure **구독** 을 선택합니다.
        1. 기존 **리소스 그룹** 을 선택하거나 리소스 그룹을 만듭니다. 
    1. **네임스페이스 세부 정보** 섹션에서 다음 단계를 수행합니다.
        1. 네임스페이스의 **이름** 을 입력합니다. 
        1. 네임스페이스의 **위치** 를 선택합니다. 
    1. **등록 세부 정보** 섹션에서 다음 단계를 수행하여 네임스페이스를 파트너 등록과 연결합니다. 
        1. 파트너 등록이 있는 **구독** 을 선택합니다. 
        1. 파트너 등록이 포함된 **자원 그룹** 을 선택합니다. 
        1. 드롭다운 목록에서 **파트너 등록** 을 선택합니다.
    1. 페이지 하단에서 **다음: 태그** 를 선택합니다.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="파트너 네임스페이스 만들기 - 기본 페이지":::
1. **태그** 페이지에서 태그를 추가합니다(선택 사항).
    1. 추가할 태그의 **이름** 및 **값** 을 입력합니다. 이 단계는 **선택 사항** 입니다.
    1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.         
1. **검토 + 만들기** 페이지에서 세부 정보를 검토하고 **만들기** 를 선택합니다. 

## <a name="create-an-event-channel"></a>이벤트 채널 만들기
> [!IMPORTANT]
> 사용자가 소유하고 관리할 파트너 토픽을 만들려면 사용자에게 Azure 구독, 리소스 그룹, 위치 및 파트너 토픽 이름을 요청해야 합니다.

1. 만든 네임스페이스의 **개요** 페이지로 이동합니다. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="파트너 네임스페이스 - 개요 페이지":::
    partner-namespace-overview.png
1. 도구 모음에서 **+ 이벤트 채널** 을 선택합니다. 
1. **이벤트 채널 만들기 - 기본** 페이지에서 다음 정보를 지정합니다. 
    1. **채널 세부 정보** 섹션에서 다음 단계를 수행합니다.
        1. **이벤트 채널 이름** 에 이벤트 채널의 이름을 입력합니다. 
        1. **원본** 을 입력합니다. 원본에 적합한 값에 대한 아이디어를 얻으려면 [Cloud Events 1.0 사양](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1)을 참조하세요. 또한 [이 Cloud Events 스키마 예](cloud-event-schema.md#sample-event-using-cloudevents-schema)를 참조하세요.
    1. **대상 세부 정보** 섹션에서 이 이벤트 채널에 대해 만들어질 대상 파트너 토픽에 대한 세부 정보를 입력합니다. 
        1. 파트너 토픽이 만들어질 **구독 ID** 를 입력합니다. 
        1. 파트너 토픽 리소스가 만들어질 **리소스 그룹의 이름** 을 입력합니다. 
        1. **파트너 토픽의 이름** 을 입력합니다. 
    1. 페이지 하단에서 **다음: 필터** 를 선택합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="이벤트 채널 만들기 - 기본 페이지":::
1. **필터** 페이지에서 필터를 추가합니다. 다음 단계를 수행합니다.
    1. 각 이벤트의 특성을 필터링합니다. 모든 필터와 일치하는 이벤트만 배달됩니다. 최대 25개의 필터를 지정할 수 있습니다. 비교는 대/소문자를 구분하지 않습니다. 필터에 사용되는 유효한 키는 이벤트 스키마에 따라 다릅니다. 다음 예에서 `eventid`, `source`, `eventtype` 및 `eventtypeversioin`을 키로 사용할 수 있습니다. 중첩 연산자로 `.`을 사용하여 데이터 페이로드 내에서 사용자 정의 속성을 사용할 수도 있습니다. 예: `data`, `data.key`, `data.key1.key2`.
    1. 페이지 하단에서 **다음: 추가 기능** 을 선택합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="이벤트 채널 만들기 - 필터 페이지":::
        create-event-channel-filters-page.png
1. **추가 기능** 페이지에서 **만료 시간** 및 **파트너 토픽에 대한 설명** 을 설정할 수 있습니다. 
    1. **만료 시간** 은 고객이 활성화하지 않은 경우 토픽 및 관련 이벤트 채널이 자동으로 삭제되는 시간입니다. 시간이 제공되지 않는 경우 기본값은 7일로 사용됩니다. 사용자 고유의 만료 시간을 지정하려면 이 확인란을 선택합니다. 
    1. 이 토픽은 사용자가 작성하지 않은 리소스이므로 **설명** 을 통해 사용자가 이 토픽의 특성을 이해할 수 있습니다. 설정되지 않은 경우에는 일반 설명이 제공됩니다. 사용자의 파트너 토픽 설명을 설정하려면 이 확인란을 선택합니다. 
    1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="이벤트 채널 만들기 - 추가 기능 페이지":::
1. **검토 + 만들기** 에서 설정을 검토하고 **만들기** 를 선택하여 이벤트 채널을 만듭니다. 

## <a name="next-steps"></a>다음 단계
- [파트너 토픽 개요](./partner-events-overview.md)
- [파트너 토픽 온보딩 양식](https://aka.ms/gridpartnerform)
- [Auth0 파트너 토픽](auth0-overview.md)
- [Auth0 파트너 토픽을 사용하는 방법](auth0-how-to.md)