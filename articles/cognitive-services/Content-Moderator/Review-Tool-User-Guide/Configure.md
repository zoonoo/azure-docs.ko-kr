---
title: 검토 도구 설정 구성-Content Moderator
titleSuffix: Azure Cognitive Services
description: 검토 도구를 사용 하 여 Content Moderator에 대 한 팀, 태그, 커넥터, 워크플로 및 자격 증명을 구성 하거나 검색할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84689846"
---
# <a name="configure-the-review-tool"></a>검토 도구 구성

[검토 도구](https://contentmoderator.cognitive.microsoft.com) 에는 대시보드의 **설정** 메뉴를 통해 액세스할 수 있는 몇 가지 중요 한 기능이 있습니다.

![Content Moderator 검토 너무 설정 메뉴](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>팀 및 하위 팀 관리

**팀** 탭에서는 &mdash; 특정 사용자 [검토가](../review-api.md#reviews) 시작 될 때 알릴 수 있는 팀 및 하위 팀 그룹을 관리할 수 있습니다. 검토 도구를 사용 하 여 등록할 때 만든 팀은 하나만 포함할 수 있지만 여러 하위 팀을 만들 수 있습니다. 팀 관리자는 구성원을 초대 하 고, 사용 권한을 설정 하 고, 다른 하위 팀에 할당할 수 있습니다.

![검토 도구 팀 설정](images/settings-2-team.png)

하위 팀은 에스컬레이션 팀 또는 콘텐츠의 특정 범주를 검토하는 전담 팀을 만들 때 유용합니다. 예를 들어 추가 검토를 위해 성인 콘텐츠를 별도의 팀에 보낼 수 있습니다.

이 섹션에서는 하위 팀을 만들고 즉시 리뷰를 신속 하 게 할당 하는 방법을 설명 합니다. 하지만 [워크플로](workflows.md)를 사용하여 특정 기준에 따라 검토를 할당할 수도 있습니다.

### <a name="create-a-subteam"></a>하위 팀 만들기

하위 **팀** 섹션으로 이동 하 고 **만듦 추가**를 클릭 합니다. 대화 상자에 만듦 이름을 입력 하 고 **저장**을 클릭 합니다.

![하위 팀 이름](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>동료 초대

사용자가 아직 기본 팀의 멤버가 아닌 경우 해당 사용자를 만듦에 할당할 수 없으므로 먼저 기본 팀에 검토자를 추가 해야 합니다. **팀** 탭에서 **초대** 를 클릭 합니다.

![사용자 초대](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>만듦에 팀 동료 할당

**멤버 추가** 단추를 클릭 하 여 기본 팀의 멤버를 하나 이상의 하위 팀에 할당 합니다. 기존 사용자만 하위 팀에 추가할 수 있습니다. 검토 도구에 없는 새 사용자를 추가하려면 [팀 설정] 페이지에서 "초대" 단추를 사용하여 해당 사용자를 초대해야 합니다.

![하위 팀 멤버 할당](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>하위 팀에 리뷰 할당

하위 팀 및 할당 된 멤버를 만든 후에는 해당 하위 팀에 콘텐츠 [검토](../review-api.md#reviews) 할당을 시작할 수 있습니다. 이 작업은 사이트의 **검토** 탭에서 수행 합니다.
만듦에 콘텐츠를 할당 하려면 오른쪽 위 모서리에서 줄임표를 클릭 하 고 **이동**을 선택한 다음 만듦를 선택 합니다.

![하위 팀에 이미지 검토 할당](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>하위 팀 간에 전환

둘 이상의 만듦의 멤버인 경우 해당 하위 팀 간에 전환 하 여 표시 되는 내용 검토를 변경할 수 있습니다. **검토** 탭에서 **기본** 이라는 레이블이 지정 된 드롭다운 메뉴를 선택 하 고 **만듦 선택**을 선택 합니다. 다른 하위 팀에 대 한 콘텐츠 검토를 볼 수 있지만 해당 하위 팀의 콘텐츠 검토는 볼 수 있습니다.

![하위 팀 간에 전환](images/3-review-image-subteam-2.png)

## <a name="tags"></a>태그들

**태그** 탭에서는 두 개의 기본 중재 태그 &mdash; **isadult** (**a**) 및 **isracy** (**r**)와 함께 사용자 지정 중재 태그를 정의할 수 있습니다. 사용자 지정 태그를 만들 때 기본 태그와 함께 검토에서 사용할 수 있게 됩니다. 표시 유형 설정을 전환 하 여 리뷰에 표시 되는 태그를 변경할 수 있습니다.

![태그 보기 ("표시 됨" 확인란 포함)](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>사용자 지정 태그 만들기

새 태그를 만들려면 해당 필드에 짧은 코드, 이름 및 설명을 입력 해야 합니다.

- **Short code**: 태그에 대해 두 문자로 된 코드를 입력 합니다. 예: **cb**
- **이름**: 공백 없이 소문자에 짧은 설명 태그 이름을 입력 합니다. 예: **is인**
- **설명**: (선택 사항) 태그가 대상으로 하는 콘텐츠 종류에 대 한 설명을 입력 합니다. 예: **사이버 인의 묘사 또는 인스턴스**

**추가** 를 클릭 하 여 태그를 추가 하 고 태그 만들기를 마치면 **저장** 을 클릭 합니다.

![검토 도구 새 태그 만들기 대화 상자](images/settings-3-tags.png)

### <a name="delete-tags"></a>태그 삭제

태그 목록에서 해당 항목 옆에 있는 휴지통 아이콘을 선택 하 여 사용자 지정 태그를 삭제할 수 있지만 기본 태그는 삭제할 수 없습니다.

## <a name="connectors"></a>커넥터

**커넥터** 탭을 사용 하면 콘텐츠 [워크플로의](../review-api.md#workflows)일부로 다양 한 방법으로 콘텐츠를 처리할 수 있는 서비스별 플러그인 인 커넥터를 관리할 수 있습니다.

워크플로를 만들 때 기본 커넥터는 Content Moderator 커넥터 이며, 콘텐츠를 **성인** 또는 **외설**로 표시 하 고, 비속어를 찾을 수 있습니다. 그러나 여기에 나열 된 다른 커넥터를 사용할 수 있습니다. 각 서비스에 대 한 자격 증명을 사용 하는 경우 (예 [: 얼굴 연결](https://docs.microsoft.com/azure/cognitive-services/face/overview) 키를 받아야 하는 경우), 얼굴 커넥터를 사용 합니다.

[검토 도구](./human-in-the-loop.md) 에는 다음 커넥터가 포함 되어 있습니다.

- Emotion
- Face
- PhotoDNA 클라우드 서비스
- Text Analytics

### <a name="add-a-connector"></a>커넥터 추가

커넥터를 추가 하 고 콘텐츠 [워크플로에서](../review-api.md#workflows)사용할 수 있도록 하려면 적절 한 **연결** 단추를 선택 합니다. 다음 대화 상자에서 해당 서비스에 대 한 구독 키를 입력 합니다. 완료 되 면 새 커넥터가 페이지 맨 위에 표시 됩니다.

![Content Moderator 커넥터 설정](images/settings-4-connectors.png)

## <a name="workflows"></a>워크플로

**워크플로** 탭에서 [워크플로](../review-api.md#workflows)를 관리할 수 있습니다. 워크플로는 콘텐츠에 대 한 클라우드 기반 필터로, 커넥터를 사용 하 여 다양 한 방법으로 콘텐츠를 정렬 하 고 적절 한 조치를 취합니다. 여기에서 워크플로를 정의 하 고, 편집 하 고, 테스트할 수 있습니다. 이 작업을 수행 하는 방법에 대 한 지침은 [워크플로 정의 및 사용](Workflows.md) 을 참조 하세요.

![Content Moderator 워크플로 설정](images/settings-5-workflows.png)

## <a name="credentials"></a>자격 증명

**자격 증명** 탭에서는 REST 호출 또는 클라이언트 SDK에서 중재 서비스에 액세스 해야 하는 Content Moderator 구독 키에 빠르게 액세스할 수 있습니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>워크플로에 대 한 외부 자격 증명 사용

[검토 도구](https://contentmoderator.cognitive.microsoft.com) 는 등록할 때 azure Content Moderator 서비스에 대 한 무료 평가판 키를 생성 하지만, azure 계정의 기존 키를 사용 하도록 구성할 수도 있습니다. 이는 무료 평가판 키에 엄격한 사용 제한 ([가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/))이 있으므로 대규모 시나리오에 권장 됩니다.

Azure에서 [Content Moderator 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) 를 만든 경우 Azure Portal에서 이동 하 여 **키** 블레이드를 선택 합니다. 키 중 하나를 복사 합니다.

![Azure Portal의 Content Moderator 키](images/credentials-azure-portal-keys.PNG)

[검토 도구의](https://contentmoderator.cognitive.microsoft.com) **자격 증명** 탭에서 **워크플로 설정** 창으로 이동 하 여 **편집**을 선택 하 고 키를 **Ocp-apim-구독 키** 필드에 붙여넣습니다. 이제 중재 Api를 호출 하는 워크플로는 Azure 자격 증명을 사용 합니다.

> [!NOTE]
> **워크플로 설정** 창의 다른 두 필드는 사용자 지정 용어 및 이미지 목록에 대 한 것입니다. 이러한 항목에 대 한 자세한 내용은 [사용자 지정 용어](../try-terms-list-api.md) 또는 [사용자 지정 이미지](../try-image-list-api.md) 가이드를 참조 하세요.

### <a name="use-your-azure-account-with-the-review-apis"></a>검토 Api와 함께 Azure 계정 사용

검토 Api와 함께 Azure 키를 사용 하려면 리소스 ID를 검색 해야 합니다. Azure Portal에서 Content Moderator 리소스로 이동 하 여 **속성** 블레이드를 선택 합니다. 리소스 ID 값을 복사 하 여 검토 도구의 **자격 증명** 탭에 있는 **허용 목록 리소스 id** 필드에 붙여넣습니다.

![Azure Portal의 Content Moderator 리소스 ID](images/credentials-azure-portal-resourceid.PNG)

두 위치 모두에 구독 키를 입력 한 경우 검토 도구 계정과 함께 제공 되는 평가판 키는 사용 되지 않지만 사용 가능한 상태로 유지 됩니다.

## <a name="next-steps"></a>다음 단계

[검토 도구 빠른](../quick-start.md) 시작을 따라 콘텐츠 조정 시나리오에서 검토 도구 사용을 시작 합니다.