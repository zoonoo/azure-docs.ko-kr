---
title: Content Moderator API에서 팀 및 하위 팀 관리 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator API의 팀 및 하위 팀을 Cognitive Services에 사용하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: a36da1787d5abd5f7dc1455823be55f1880c7ba5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227134"
---
# <a name="manage-review-teams-and-subteams"></a>검토 팀 및 하위 팀 관리

Content Moderator를 사용하려면 먼저 팀을 만들어야 합니다. 팀을 등록하고 팀 이름을 지정하면 해당 팀은 기본 팀이 됩니다. 검토 도구에서 허용되는 팀은 하나입니다. 하지만 여러 개의 하위 팀을 만들 수 있습니다. 하위 팀은 에스컬레이션 팀 또는 콘텐츠의 특정 범주를 검토하는 전담 팀을 만들 때 유용합니다. 예를 들어 자세한 검토를 위해 성인 등급 콘텐츠를 다른 팀에 보내야 하는 경우가 있습니다.

이 토픽에서는 하위 팀을 만들고, 필요할 때 신속하게 검토를 할당하는 방법을 설명합니다. 하지만 [워크플로](workflows.md)를 사용하여 특정 기준에 따라 검토를 할당할 수도 있습니다.

## <a name="go-to-the-teams-setting"></a>팀 설정으로 이동

하위 팀 만들기를 시작하려면 [설정] 아래에서 **팀** 옵션을 선택합니다.

![팀 설정](images/0-teams-1.png)

## <a name="create-subteams"></a>하위 팀 만들기

기본 팀에는 모든 잠재적 검토자가 포함되고, 하위 팀은 기본 팀의 하위 집합입니다. 기본 팀의 멤버가 아닌 사람을 하위 팀에 할당할 수 없으므로 일단은 검토자를 기본 팀에 추가해야 합니다. [팀 페이지]에서 [초대] 단추를 클릭합니다.

![사용자 초대](images/invite-users.png)

### <a name="create-a-subteam"></a>하위 팀 만들기
[하위 팀] 섹션이 나올 때까지 [팀 페이지]를 아래로 스크롤합니다. [하위 팀 추가] 단추를 클릭합니다. 

![하위 팀 추가](images/1-teams-1.png)

### <a name="name-your-subteam"></a>하위 팀 이름 지정
대화 상자에서 하위 팀 이름을 입력하고 [저장]을 클릭합니다.

![하위 팀 이름](images/1-Teams-2.PNG)

### <a name="assign-members-from-your-default-team"></a>기본 팀의 멤버를 할당합니다.
[멤버 추가] 단추를 클릭하여 기본 팀의 멤버를 하나 이상의 하위 팀에 할당합니다. 기존 사용자만 하위 팀에 추가할 수 있습니다. 검토 도구에 없는 새 사용자를 추가하려면 [팀 설정] 페이지에서 "초대" 단추를 사용하여 해당 사용자를 초대해야 합니다.

![하위 팀 멤버 할당](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>하위 팀에 검토 할당

하위 팀을 만들고 팀 멤버를 할당했으면 해당 하위 팀에 이미지 및 텍스트 검토를 할당할 수 있습니다. 이 작업은 [검토] 창에서 수행합니다.
하위 팀에 개별 이미지를 할당하려는 경우 이미지 오른쪽 위 모서리에 있는 줄임표를 클릭하고, [다음으로 이동]을 선택하고, 하위 팀을 선택합니다.

![하위 팀에 이미지 검토 할당](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>하위 팀을 전환하여 할당된 콘텐츠 검토

하나 또는 여러 하위 팀의 멤버인 경우 검토 도구 대시보드에서 하위 팀 간에 전환할 수 있습니다. 하위 팀에 속한 현재 보류 중인 모든 검토를 보려면 [이미지] 탭에서 [하위 팀 선택]을 선택합니다.

![하위 팀 간에 전환](images/3-review-image-subteam-2.png)
