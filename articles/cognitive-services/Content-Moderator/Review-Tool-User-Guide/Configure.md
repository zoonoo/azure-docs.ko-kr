---
title: 검토 도구 설정 구성 - 콘텐츠 중재자
titleSuffix: Azure Cognitive Services
description: 검토 도구를 사용하여 콘텐츠 중재자에 대한 팀, 태그, 커넥터, 워크플로 및 자격 증명을 구성하거나 검색할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221354"
---
# <a name="configure-the-review-tool"></a>검토 도구 구성

[검토 도구에는](https://contentmoderator.cognitive.microsoft.com) 대시보드의 **설정** 메뉴를 통해 액세스할 수 있는 몇 가지 중요한 기능이 있습니다.

![콘텐츠 진행자 검토 너무 설정 메뉴](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>팀 및 하위 팀 관리

**팀** 탭을 사용하면 특정 사용자&mdash;검토가 시작될 때 알림을 받을 수 있는 팀 및 하위 팀 사용자 그룹을 관리할 수 [있습니다.](../review-api.md#reviews) 검토 도구로 등록할 때 만드는 팀한 팀만 가질 수 있지만 여러 하위 팀을 만들 수 있습니다. 팀 관리자는 구성원을 초대하고, 권한을 설정하고, 다른 하위 팀에 할당할 수 있습니다.

![도구 팀 설정 검토](images/settings-2-team.png)

하위 팀은 에스컬레이션 팀 또는 콘텐츠의 특정 범주를 검토하는 전담 팀을 만들 때 유용합니다. 예를 들어 추가 검토를 위해 성인 용 콘텐츠를 별도의 팀으로 보낼 수 있습니다.

이 섹션에서는 하위 팀을 만들고 즉석에서 리뷰를 신속하게 할당하는 방법을 설명합니다. 하지만 [워크플로](workflows.md)를 사용하여 특정 기준에 따라 검토를 할당할 수도 있습니다.

### <a name="create-a-subteam"></a>하위 팀 만들기

**하위 팀** 섹션으로 이동하여 하위 **팀 추가를**클릭합니다. 대화 상자에 하위 팀 이름을 입력하고 **저장을**클릭합니다.

![하위 팀 이름](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>팀원 초대

하위 팀에 다른 사람을 할당할 수 없는 경우 기본 팀의 구성원이 아닌 경우 먼저 기본 팀에 검토자를 추가해야 합니다. **팀** 탭에서 **초대를** 클릭합니다.

![사용자 초대](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>하위 팀에 팀원 할당

구성원 **추가** 단추를 클릭하여 기본 팀의 구성원을 하나 이상의 하위 팀에 할당합니다. 기존 사용자만 하위 팀에 추가할 수 있습니다. 검토 도구에 없는 새 사용자를 추가하려면 [팀 설정] 페이지에서 "초대" 단추를 사용하여 해당 사용자를 초대해야 합니다.

![하위 팀 멤버 할당](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>하위 팀에 리뷰 할당

하위 팀을 만들고 구성원을 할당한 후에는 해당 하위 팀에 콘텐츠 [검토를](../review-api.md#reviews) 할당할 수 있습니다. 이 작업은 사이트의 **검토** 탭에서 수행됩니다.
하위 팀에 콘텐츠를 할당하려면 오른쪽 상단 모서리에 있는 타원을 클릭하고 로 **이동을**선택하고 하위 팀을 선택합니다.

![하위 팀에 이미지 검토 할당](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>하위 팀 간에 전환

둘 이상의 하위 팀의 구성원인 경우 해당 하위 팀 간에 전환하여 표시되는 콘텐츠 리뷰를 변경할 수 있습니다. **검토** 탭에서 **기본값으로** 표시된 드롭다운 메뉴를 선택하고 **하위 팀 선택을**선택합니다. 다른 하위 팀에 대한 콘텐츠 리뷰를 볼 수 있지만 회원인 팀만 볼 수 있습니다.

![하위 팀 간에 전환](images/3-review-image-subteam-2.png)

## <a name="tags"></a>태그들

**태그** 탭을 사용하면 두 개의 기본 중재&mdash;태그인**성인(a)** 및 isracy(r)에**r**추가하여 사용자 지정 중재 태그를 정의할 수**있습니다.** **isracy** 사용자 지정 태그를 만들면 기본 태그와 함께 리뷰에서 사용할 수 있습니다. 게재 범위 설정을 전환하여 리뷰에 표시되는 태그를 변경할 수 있습니다.

!["표시 중" 확인란을 포함한 태그 보기](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>사용자 지정 태그 만들기

새 태그를 만들려면 각 필드에 짧은 코드, 이름 및 설명을 입력해야 합니다.

- **단축 코드**: 태그에 두 글자로 된 코드를 입력합니다. 예: **cb**
- **이름**: 공백없이 소문자로 짧고 설명적인 태그 이름을 입력합니다. 예: **괴롭히는**.
- **설명**: (선택 사항) 태그가 타겟팅하는 콘텐츠 의 종류에 대한 설명을 입력합니다. 예: **사이버 괴롭힘의 묘사 또는 인스턴스.**

태그를 추가하려면 **추가를** 클릭하고 태그 만들기가 완료되면 **저장을** 클릭합니다.

![검토 도구 만들기 새 태그 대화 상자](images/settings-3-tags.png)

### <a name="delete-tags"></a>태그 삭제

태그 목록의 항목 옆에 있는 휴지통 아이콘을 선택하여 사용자 지정 태그를 삭제할 수 있지만 기본 태그는 삭제할 수 없습니다.

## <a name="connectors"></a>커넥터

**커넥터 탭을** 사용하면 콘텐츠 [워크플로의](../review-api.md#workflows)일부로 다른 방식으로 콘텐츠를 처리할 수 있는 서비스별 플러그인인 커넥터를 관리할 수 있습니다.

워크플로를 만들 때 기본 커넥터는 콘텐츠를 **성인** 또는 **외설성으로**표시하고 욕설을 찾을 수 있는 콘텐츠 중재자 커넥터입니다. 그러나 각 서비스에 대한 자격 증명이 있는 경우 여기에 나열된 다른 커넥터를 사용할 수 있습니다(예: Face 커넥터를 사용하려면 [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview) 구독 키를 받아야 합니다).

[검토 도구에는](./human-in-the-loop.md) 다음 커넥터가 포함되어 있습니다.

- Emotion
- Face
- PhotoDNA 클라우드 서비스
- 텍스트 분석

### <a name="add-a-connector"></a>커넥터 추가

커넥터를 추가하고 콘텐츠 워크플로에서 사용할 수 있도록 하려면 적절한 **연결** [단추를 선택합니다.](../review-api.md#workflows) 다음 대화 상자에서 해당 서비스에 대한 구독 키를 입력합니다. 작업이 완료되면 새 커넥터가 페이지 상단에 나타납니다.

![Content Moderator 커넥터 설정](images/settings-4-connectors.png)

## <a name="workflows"></a>워크플로

**워크플로 탭을** 사용하면 [워크플로를](../review-api.md#workflows)관리할 수 있습니다. 워크플로는 콘텐츠에 대한 클라우드 기반 필터이며 커넥터와 함께 작동하여 다양한 방식으로 콘텐츠를 정렬하고 적절한 조치를 취합니다. 여기에서 워크플로를 정의, 편집 및 테스트할 수 있습니다. 이 작업을 수행하는 방법에 대한 지침은 [워크플로 정의 및 사용을](Workflows.md) 참조하십시오.

![Content Moderator 워크플로 설정](images/settings-5-workflows.png)

## <a name="credentials"></a>자격 증명

**자격 증명** 탭은 REST 호출 또는 클라이언트 SDK의 중재 서비스에 액세스해야 하는 콘텐츠 중재자 구독 키에 대한 빠른 액세스를 제공합니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>워크플로에 외부 자격 증명 사용

[검토 도구는](https://contentmoderator.cognitive.microsoft.com) 등록할 때 Azure 콘텐츠 중재자 서비스에 대한 무료 평가판 키를 생성하지만 Azure 계정의 기존 키를 사용하도록 구성할 수도 있습니다. 무료 평가판 키에는 엄격한 사용 제한(가격 및 제한)이 있기 때문에 대규모 시나리오에[권장됩니다.](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Azure에서 콘텐츠 [중재자 리소스를](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) 만든 경우 Azure 포털에서 콘텐츠 중재자 리소스로 이동하여 **키** 블레이드를 선택합니다. 키 중 하나를 복사합니다.

![Azure Portal의 Content Moderator 키](images/credentials-azure-portal-keys.PNG)

검토 [도구의](https://contentmoderator.cognitive.microsoft.com) **자격 증명** 탭에서 **워크플로 설정** 창으로 이동하여 **편집을**선택하고 키를 **Ocp-Apim-구독-키** 필드에 붙여넣습니다. 이제 중재 API를 호출하는 워크플로에서는 Azure 자격 증명을 사용합니다.

> [!NOTE]
> **워크플로 설정** 창의 다른 두 필드는 사용자 지정 용어 및 이미지 목록용입니다. [이에](../try-terms-list-api.md) 대해 자세히 알아보려면 맞춤 용어 또는 [맞춤 이미지](../try-image-list-api.md) 가이드를 참조하세요.

### <a name="use-your-azure-account-with-the-review-apis"></a>검토 API와 함께 Azure 계정 사용

검토 API와 함께 Azure 키를 사용하려면 리소스 ID를 검색해야 합니다. Azure 포털의 콘텐츠 중재자 리소스로 이동하여 **속성** 블레이드를 선택합니다. 리소스 ID 값을 복사하여 검토 도구의 자격 증명 탭의 **화이트리스트 리소스 ID** 필드에 붙여넣습니다. **Credentials**

![Azure Portal의 Content Moderator 리소스 ID](images/credentials-azure-portal-resourceid.PNG)

두 곳 모두에서 구독 키를 입력한 경우 검토 도구 계정과 함께 제공되는 평가판 키는 사용되지 않지만 사용 가능한 상태로 유지됩니다.

## <a name="next-steps"></a>다음 단계

검토 [도구 빠른 시작을](../quick-start.md) 따라 콘텐츠 조정 시나리오에서 검토 도구를 사용하기 시작합니다.