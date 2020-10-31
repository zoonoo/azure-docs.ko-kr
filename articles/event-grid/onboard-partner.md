---
title: Azure Portal를 사용 하 여 Azure Event Grid 파트너로 등록
description: Azure Portal를 사용 하 여 Azure Event Grid 파트너를 등록 합니다.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 36fab35923b8a536a9054e5dc4bfa4c5b82172a7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102916"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Event Grid 파트너로 등록
이 문서에서는 이벤트 게시자 또는 파트너 라고도 하는 타사 SaaS 공급자를 사용 하 여 서비스에서 이벤트를 게시 하 고 최종 사용자가 이러한 이벤트를 사용 하는 방법을 등록 Event Grid 하는 방법을 설명 합니다.

> [!IMPORTANT]
> 파트너 이벤트에 익숙하지 않은 경우이 문서의 단계를 이해 하 고 수행 하는 데 중요 한 주요 개념에 대 한 자세한 소개는 [파트너 이벤트 개요](partner-events-overview.md) 를 참조 하세요.

## <a name="onboarding-process-for-event-publishers-partners"></a>이벤트 게시자 (파트너)를 위한 온 보 딩 프로세스
간단히 말해서 사용자가 서비스의 이벤트를 사용 하도록 설정 하는 작업은 일반적으로 다음 프로세스를 포함 합니다.

1. 다음 단계를 진행 하기 전에 Event Grid 서비스 팀에 파트너가 되는 것에 **관심을 전달** 합니다.
1. **등록** 을 만들어 파트너 토픽 유형을 만듭니다. 
1. **네임 스페이스** 를 만듭니다.
1. **이벤트 채널** 및 **파트너 항목** (단일 단계)을 만듭니다.
1. 종단 간 파트너 이벤트 기능을 테스트 합니다.

#4 단계에서는 제공 하려는 사용자 환경의 종류를 결정 해야 합니다. 다음과 같은 옵션을 선택할 수 있습니다.
- SDK 및/또는 REST API를 사용 하 여 도메인에 호스트 되는 웹 GUI (그래픽 사용자 인터페이스) 환경에서 사용자 고유의 솔루션을 제공 하 여 이벤트 채널과 해당 하는 파트너 항목을 만듭니다. 이 옵션을 사용 하 여 파트너 토픽을 만들 구독 및 리소스 그룹을 사용자에 게 요청할 수 있습니다.
- Azure Portal 또는 CLI를 사용 하 여 이벤트 채널과 연결 된 파트너 항목을 만듭니다. 이 옵션을 사용 하는 경우 사용자의 Azure 구독에 파트너 토픽을 만드는 일부 방법 및 리소스 그룹이 있어야 합니다. 

이 문서에서는 Azure Portal를 사용 하 여 Azure Event Grid 파트너로 등록 하는 방법을 보여 줍니다. 

> [!NOTE]
> 파트너 토픽 유형을 등록 하는 단계는 선택 사항입니다. 파트너 토픽 유형을 만들어야 하는지 결정 하는 데 도움이 되도록 [이벤트 게시자에서 관리](partner-events-overview.md#resources-managed-by-event-publishers)하는 리소스를 참조 하세요.

## <a name="communicate-your-interest-in-becoming-a-partner"></a>파트너에 대 한 관심을 전달 합니다.
[이 양식을](https://aka.ms/gridpartnerform) 작성 하 고 Event Grid 팀에 문의 하세요 [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . 파트너 이벤트의 사용 사례, 가상 사용자, 온 보 딩 프로세스, 기능, 가격 및 기타에 대 한 자세한 정보를 제공 하는 대화가 제공 됩니다.

## <a name="prerequisites"></a>필수 구성 요소
나머지 단계를 완료 하려면 다음을 수행 해야 합니다.

- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- Azure [테 넌 트](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="register-a-partner-topic-type-optional"></a>파트너 토픽 유형 등록 (선택 사항)
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 창에서 **모든 서비스** 를 선택 하 고 검색 표시줄에 **Event Grid 파트너 등록** 을 입력 한 다음 선택 합니다. 
1. **Event Grid 파트너 등록** 페이지의 도구 모음에서 **+ 추가** 를 선택 합니다. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="파트너 등록 링크 추가":::
1. **파트너 만들기 항목 유형 등록-기본 사항** 페이지에서 다음 정보를 입력 합니다. 
    1. **프로젝트 세부 정보** 섹션에서 다음 단계를 수행 합니다.
        1. Azure **구독** 을 선택합니다. 
        1. 기존 Azure **리소스 그룹** 을 선택 하거나 새 리소스 그룹을 만듭니다. 
    1. **등록 세부 정보** 섹션에서 다음 단계를 수행 합니다.
        1. **등록 이름** 에 등록에 대 한 이름을 입력 합니다. 
        1. **조직 이름** 에 조직의 이름을 입력 합니다. 
    1. **파트너 리소스 유형** 섹션에서 **파트너 토픽 만들기** 페이지에 표시 되는 리소스 유형에 대 한 세부 정보를 입력 합니다. 
        1. **파트너 리소스 유형 이름** 에 리소스 유형의 이름을 입력 합니다. Azure 구독에서 생성 되는 파트너 토픽의 유형입니다. 
        2. **표시 이름** 에는 파트너 토픽 (리소스) 유형에 대 한 사용자에 게 친숙 한 표시 이름을 입력 합니다. 
        3. **리소스 종류에 대 한 설명을** 입력 합니다. 
        4. **시나리오에 대 한 설명을** 입력 합니다. 리소스에 대 한 파트너 항목을 사용할 수 있는 방법 또는 시나리오를 설명 해야 합니다.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="파트너 등록 링크 추가":::            
1. 페이지 맨 아래에서 **다음: 사용자 지정 서비스** 를 선택 합니다. **파트너 등록 만들기** 페이지의 **고객 서비스** 탭에서, 이벤트 원본에 문제가 발생 하는 경우 구독자가 사용자에 게 연락 하는 데 사용할 정보를 입력 합니다.
    1. **전화 번호** 를 입력 합니다.
    1. 전화 번호에 대 한 **내선** 번호를 입력 합니다.
    1. 지원 웹 사이트 **URL** 을 입력 하십시오. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="파트너 등록 링크 추가":::        
1. 페이지 맨 아래에 있는 **다음: 태그** 를 선택 합니다. 
1. **태그** 페이지에서 다음 값을 구성 합니다. 
    1. 추가할 태그의 **이름** 및 **값** 을 입력합니다. 이 단계는 **선택 사항** 입니다. 
    1. 페이지 맨 아래에서 **검토 + 만들기** 를 선택 하 여 등록 (파트너 토픽 유형)을 만듭니다.

## <a name="create-a-partner-namespace"></a>파트너 네임 스페이스 만들기

1. Azure Portal의 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택 하 고 검색 표시줄에 **Event Grid Partner 네임 스페이스** 를 입력 한 다음 목록에서 선택 합니다. 
1. **Event Grid 파트너 네임 스페이스** 페이지의 도구 모음에서 **+ 추가** 를 선택 합니다. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="파트너 등록 링크 추가":::
1. **파트너 네임 스페이스-기본 사항 만들기** 페이지에서 다음 정보를 지정 합니다.
    1. **프로젝트 세부 정보** 섹션에서 다음 단계를 수행 합니다. 
        1. Azure **구독** 을 선택 합니다.
        1. 기존 **리소스 그룹** 을 선택 하거나 리소스 그룹을 만듭니다. 
    1. **네임 스페이스 세부 정보** 섹션에서 다음 단계를 수행 합니다.
        1. 네임스페이스의 **이름** 을 입력합니다. 
        1. 네임스페이스의 **위치** 를 선택합니다. 
    1. **등록 정보** 섹션에서 다음 단계를 수행 하 여 네임 스페이스를 파트너 등록과 연결 합니다. 
        1. 파트너 등록이 존재 하는 **구독** 을 선택 합니다. 
        1. 파트너 등록을 포함 하는 **리소스 그룹** 을 선택 합니다. 
        1. 드롭다운 목록에서 **파트너 등록** 을 선택 합니다.
    1. 페이지 맨 아래에 있는 **다음: 태그** 를 선택 합니다.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="파트너 등록 링크 추가":::
1. **태그** 페이지에서 태그를 추가 합니다 (선택 사항).
    1. 추가할 태그의 **이름** 및 **값** 을 입력합니다. 이 단계는 **선택 사항** 입니다.
    1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.         
1. **검토 + 만들기** 페이지에서 세부 정보를 검토 하 고 **만들기** 를 선택 합니다. 

## <a name="create-an-event-channel"></a>이벤트 채널 만들기
> [!IMPORTANT]
> 사용자가 소유 하 고 관리할 파트너 토픽을 만들려면 사용자에 게 Azure 구독, 리소스 그룹, 위치 및 파트너 항목 이름을 요청 해야 합니다.

1. 만든 네임 스페이스의 **개요** 페이지로 이동 합니다. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="파트너 등록 링크 추가":::
    partner-namespace-overview.png
1. 도구 모음에서 **+ 이벤트 채널** 을 선택 합니다. 
1. **이벤트 채널 만들기-기본 사항** 페이지에서 다음 정보를 지정 합니다. 
    1. **채널 정보** 섹션에서 다음 단계를 수행 합니다.
        1. **이벤트 채널 이름** 에 이벤트 채널의 이름을 입력 합니다. 
        1. **원본을** 입력 합니다. 원본에 적합 한 값을 이해 하려면 [클라우드 이벤트 1.0 사양](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) 을 참조 하세요. 또한 [이 클라우드 이벤트 스키마 예제](cloud-event-schema.md#sample-event-using-cloudevents-schema)를 참조 하세요.
        1. 원본 (이란?)을 입력 합니다.
    1. **대상 세부 정보** 섹션에서이 이벤트 채널에 대해 생성 될 대상 파트너 토픽의 세부 정보를 입력 합니다. 
        1. 파트너 토픽을 만들 **구독의 ID** 를 입력 합니다. 
        1. 파트너 토픽 리소스를 만들 **리소스 그룹의 이름을** 입력 합니다. 
        1. **파트너 항목의 이름을** 입력 합니다. 
    1. 페이지 맨 아래에서 **다음: 필터** 를 선택 합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="파트너 등록 링크 추가":::
1. **필터** 페이지에서 필터를 추가 합니다. 다음 단계를 수행합니다.
    1. 각 이벤트의 특성을 필터링 합니다. 모든 필터와 일치 하는 이벤트만 배달 됩니다. 최대 25 개의 필터를 지정할 수 있습니다. 비교는 대/소문자를 구분 하지 않습니다. 필터에 사용 되는 유효한 키는 이벤트 스키마에 따라 다릅니다. 다음 예제에서는,, `eventid` `source` 및를 `eventtype` `eventtypeversioin` 키에 사용할 수 있습니다. 를 중첩 연산자로 사용 하 여 데이터 페이로드 내에서 사용자 지정 속성을 사용할 수도 있습니다 `.` . 예: `data` , `data.key` , `data.key1.key2` .
    1. 페이지 맨 아래에 있는 **추가 기능** 을 선택 합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="파트너 등록 링크 추가":::
        create-event-channel-filters-page.png
1. **추가 기능** 페이지에서 **파트너 항목에 대 한** **만료 시간** 및 설명을 설정할 수 있습니다. 
    1. **만료 시간은** 고객이 활성화 하지 않은 경우 토픽 및 연결 된 이벤트 채널이 자동으로 삭제 되는 시간입니다. 시간이 제공 되지 않는 경우 기본값은 7 일로 사용 됩니다. 사용자 고유의 만료 시간을 지정 하려면이 확인란을 선택 합니다. 
    1. 이 항목에서는 사용자가 만들지 않은 리소스를 **설명** 하므로 사용자에 게이 항목의 특성을 이해 하는 데 도움이 될 수 있습니다. 설정이 없는 경우에는 일반 설명이 제공 됩니다. 사용자의 파트너 토픽 설명을 설정 하려면이 확인란을 선택 합니다. 
    1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="파트너 등록 링크 추가":::
1. **검토 + 만들기** 에서 설정을 검토 하 고 **만들기** 를 선택 하 여 이벤트 채널을 만듭니다. 

## <a name="next-steps"></a>다음 단계
- [파트너 토픽 개요](partner-topics-overview.md)
- [파트너 토픽 등록 양식](https://aka.ms/gridpartnerform)
- [좋아하게 되었습니다 partner 항목](auth0-overview.md)
- [좋아하게 되었습니다 partner 항목을 사용 하는 방법](auth0-how-to.md)
