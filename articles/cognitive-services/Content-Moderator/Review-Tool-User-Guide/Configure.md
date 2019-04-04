---
title: Content Moderator 검토 도구 설정-구성
titlesuffix: Azure Cognitive Services
description: 검토 도구를 사용 하 여 구성 하거나 Content Moderator에 대 한 팀, 태그, 커넥터, 워크플로 및 자격 증명을 검색 합니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756511"
---
# <a name="configure-the-review-tool"></a>검토 도구 구성

합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com) 를 통해 액세스할 수 있는 몇 가지 중요 한 기능에는 **설정** 대시보드 메뉴.

![Content Moderator 검토를 너무 설정 메뉴](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>팀 및 하위 팀 관리

합니다 **Team** 탭 팀 및 하위 팀을 관리할 수 있습니다&mdash;특정 경우에 알림을 받을 수 있는 사용자 그룹을 [사용자 검토](../review-api.md#reviews) 시작 됩니다. 하나의 팀 (검토 도구를 사용 하 여 로그인 할 때 만드는 경우)를 하나만 사용할 수 있지만 여러 하위 팀을 만들 수 있습니다. 팀 관리자 구성원을 초대 하 고 해당 사용 권한을 설정 하 고 다른 하위 팀에 할당할 수 있습니다.

![검토 도구 팀 설정](images/settings-2-team.png)

하위 팀은 에스컬레이션 팀 또는 콘텐츠의 특정 범주를 검토하는 전담 팀을 만들 때 유용합니다. 예를 들어 추가 검토를 위해 별도 팀에 성인 콘텐츠를 보낼 수 있습니다.

이 섹션에는 하위 팀을 만들고 신속 하 게 즉석에서의 검토를 할당 하는 방법을 설명 합니다. 하지만 [워크플로](workflows.md)를 사용하여 특정 기준에 따라 검토를 할당할 수도 있습니다.

### <a name="create-a-subteam"></a>하위 팀 만들기

로 이동 합니다 **하위 팀** 섹션을 클릭 **치중 추가**합니다. 대화 상자에서 하위 팀 이름을 입력 하 고 클릭 **저장할**합니다.

![하위 팀 이름](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>동료 초대

에 할당할 수 없습니다 누군가가 치중 하지 않은 경우 이미 기본 팀의 구성원 이므로 먼저 기본 팀에 검토자를 추가 해야 합니다. 클릭 **초대** 에 **팀** 탭 합니다.

![사용자 초대](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Subteam 위해 팀원을 할당

클릭 합니다 **멤버 추가** 하나 이상의 하위 팀에 기본 팀의 멤버를 할당 하는 단추입니다. 기존 사용자만 하위 팀에 추가할 수 있습니다. 검토 도구에 없는 새 사용자를 추가하려면 [팀 설정] 페이지에서 "초대" 단추를 사용하여 해당 사용자를 초대해야 합니다.

![하위 팀 멤버 할당](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>하위 팀에 할당을 검토합니다.

에 하위 팀을 만든 후 멤버에 할당 하기 콘텐츠 할당 [검토](../review-api.md#reviews) 해당 하위 팀에 있습니다. 이 작업에서 수행 되는 **검토** 사이트의 탭 합니다.
콘텐츠에 치중을 할당 하려면 오른쪽 위 모서리에서 줄임표를 클릭 **이동**, 치중을 선택 합니다.

![하위 팀에 이미지 검토 할당](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>하위 팀 간에 전환

둘 이상의 하위 팀의 구성원 인 경우에 콘텐츠 검토 표시를 변경 하려면 해당 하위 팀 간에 전환할 수 있습니다. 에 **검토** 탭에서 레이블이 지정 된 드롭다운 메뉴를 선택 **기본** 선택한 **치중 선택**합니다. 다른 하위 팀에 있는 경우에 콘텐츠 검토를 볼 수 있습니다 프로그램 구성원입니다.

![하위 팀 간에 전환](images/3-review-image-subteam-2.png)

## <a name="tags"></a>태그들

합니다 **태그** 탭에서는 두 개의 기본 조정 태그 외에도 사용자 지정 조정 태그를 정의할 수 있습니다&mdash;**isadult** (**는**) 및 **isracy**  (**r**). 사용자 지정 태그를 만들 때에 기본 태그와 함께 검토에 사용할 수 있습니다. 태그 표시 검토에서 해당 표시 설정을 전환 하 여 변경할 수 있습니다.

!["Is 표시" 확인란을 포함 하 여 태그를 보려면](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>사용자 지정 태그 만들기

새 태그를 만들려면 짧은 코드, 이름 및 설명을 해당 필드에 입력 해야 합니다.

- **코드를 짧은**: 태그에 대 한 두 문자로 된 코드를 입력 합니다. 예: **cb**
- **이름**: 단기 및 설명이 포함 된 태그 이름을 공백 없이 소문자로 입력 합니다. 예: **isbullying**합니다.
- **설명**: (선택 사항) 입력에 대 한 콘텐츠 유형 설명 하는 태그 대상입니다. 예제: **사이버 bullying 인스턴스의 나 묘사**합니다.

클릭 **추가** 태그를 추가 하 고 클릭 **저장** 태그 만들기 완료 했으면 합니다.

![검토 도구에 새 태그 대화 상자 만들기](images/settings-3-tags.png)

### <a name="delete-tags"></a>태그 삭제

태그 목록에서 해당 항목 옆에 있는 휴지통 아이콘을 선택 하 여 사용자 지정 태그를 삭제할 수 있지만 기본 태그를 삭제할 수 없습니다.

## <a name="connectors"></a>커넥터

합니다 **커넥터** 탭 콘텐츠의 일부로 다른 방법으로 콘텐츠를 처리할 수 있는 서비스 관련 플러그 인에는 커넥터를 관리할 수 있습니다 [워크플로](../review-api.md#workflows)합니다.

워크플로 만들 때 기본 커넥터는으로 콘텐츠를 표시할 수 있는 Content Moderator 커넥터 **성인** 또는 **외설**욕설을 찾아 등입니다. 그러나 각 서비스에 대 한 자격 증명이 있는으로 여기에 나열 된 다른 커넥터를 사용할 수 있습니다 (Face API 커넥터를 사용 하려면 예를 들어 해야 가져올는 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview) 구독 키).

합니다 [검토 도구](./human-in-the-loop.md) 다음 커넥터를 포함 합니다.

- Emotion API
- Face API
- PhotoDNA 클라우드 서비스
- Text Analytics API

### <a name="add-a-connector"></a>커넥터 추가

연결선을 추가 하려면 (콘텐츠에서 사용할 수 있도록 [워크플로](../review-api.md#workflows))를 적절 한 선택 **Connect** 단추입니다. 다음 대화 상자에서 해당 서비스에 대 한 구독 키를 입력 합니다. 완료 되 면 새 커넥터 페이지의 위쪽에 표시 됩니다.

![Content Moderator 커넥터 설정](images/settings-4-connectors.png)

## <a name="workflows"></a>워크플로

합니다 **워크플로** 탭 관리할 수 있습니다 하 [워크플로](../review-api.md#workflows)합니다. 워크플로 콘텐츠에 대 한 클라우드 기반 필터 및 다양 한 방법으로 콘텐츠를 정렬 하 고 적절 한 작업을 수행 하는 커넥터를 사용 하 여 작동 합니다. 이때, 같이 정의할 수 있습니다. 편집 하 고 워크플로 테스트 합니다. 참조 [정 및 사용 하 여 워크플로](Workflows.md) 이 작업을 수행 하는 방법에 대 한 지침에 대 한 합니다.

![Content Moderator 워크플로 설정](images/settings-5-workflows.png)

## <a name="credentials"></a>자격 증명

합니다 **자격 증명** 탭 REST 호출 또는 SDK 클라이언트에서 조정 서비스에 액세스 해야 하는 Content Moderator 구독 키에 대 한 빠른 액세스를 제공 합니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>워크플로에 대 한 외부 자격 증명을 사용 합니다.

합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com) Azure 계정에서 등록 한 기존 사용 하도록 구성할 수도 있습니다 하지만 Azure Content Moderator 서비스 키에 대 한 무료 평가판 키를 생성 합니다. 무료 평가판 키 엄격한 사용량 한도 대규모 시나리오에 권장 됩니다 ([가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

만들지 않은 경우는 [Content Moderator 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) Azure에서 이동 하는 Azure portal 선택에 **키** 블레이드입니다. 키 중 하나를 복사 합니다.

![Azure Portal의 Content Moderator 키](images/credentials-azure-portal-keys.PNG)

에 [검토 도구](https://contentmoderator.cognitive.microsoft.com)의 **자격 증명** 탭으로 이동 합니다 **워크플로 설정** 창 **편집**, 붙여넣습니다 넌 트 키를 **Ocp Apim-구독 키** 필드입니다. 이제 워크플로 조정 Api를 호출 하는 Azure 자격 증명을 사용 합니다.

> [!NOTE]
> 다른 두 필드를 **워크플로 설정** 창 사용자 지정 용어 및 이미지 목록에 대 한 것입니다. 참조 된 [사용자 지정 조건](../try-terms-list-api.md) 또는 [사용자 지정 이미지](../try-image-list-api.md) 이러한 대 한 자세한 가이드입니다.

### <a name="use-your-azure-account-with-the-review-apis"></a>검토 Api 사용 하 여 Azure 계정 사용

검토 Api 사용 하 여 Azure에 키를 사용 하려면 id입니다. 리소스를 검색 해야 Azure portal 선택에서 Content Moderator 리소스로 이동 합니다 **속성** 블레이드입니다. 리소스 ID 값을 복사 하 고 붙여 넣습니다 합니다 **허용 목록에 리소스 id** 검토 도구 필드 **자격 증명** 탭 합니다.

![Azure Portal의 Content Moderator 리소스 ID](images/credentials-azure-portal-resourceid.PNG)

구독 키 양쪽 모두에서 입력 한 경우 사용자 검토 도구 계정과 함께 제공 되는 평가판 키 사용 되지 않습니다 하지만 됩니다 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

에 따라 합니다 [검토 도구 빠른 시작](../quick-start.md) 검토 도구를 사용 하 여 콘텐츠 조정 시나리오에서 시작 합니다.