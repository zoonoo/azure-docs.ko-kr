---
title: Content Moderator의 검토 도구 설정 구성 | Microsoft Docs
description: 팀, 태그, 커넥터, 워크플로 및 자격 증명을 구성하거나 가져옵니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372903"
---
# <a name="about-review-tool-settings"></a>검토 도구 설정 정보 #

검토 도구 대시보드의 [설정] 탭을 사용하면 여러 구성 요소를 간편하게 정의하고 변경할 수 있습니다.

![Content Moderator 검토 설정](images/settings-1.png)

## <a name="team-and-subteams"></a>팀 및 하위 팀 ## 

이 탭에서 팀 및 하위 팀을 관리합니다. 팀은 하나만 가질 수 있지만, [여러 하위 팀을 만들고](subteams.md) 미래의 멤버에게 초대를 보낼 수 있습니다. 초대를 보낸 후에는 초대를 모니터링하고, 팀 멤버의 권한을 변경하고, 추가 사용자를 초대할 수 있습니다. 팀 멤버가 초대를 수락하면 해당 팀 멤버를 다른 하위 팀에 할당할 수 있습니다. 팀 멤버의 역할을 관리자 또는 검토자로 설정할 수 있습니다. 관리자는 다른 사용자를 초대할 수 있고, 검토자는 초대할 수 없습니다.

![Content Moderator 팀 설정](images/settings-2-team.png)

## <a name="tags"></a>태그들 ##

짧은 코드, 이름 및 태그에 대한 설명을 입력하여 [사용자 지정 태그를 정의](tags.md)할 수 있습니다. 태그가 만들어지면 검토에 사용할 수 있습니다. 표시를 켜거나 꺼서 서로 다른 검토에 서로 다른 태그를 사용할 수 있습니다.

![Content Moderator 태그 설정](images/settings-3-tags.png)

## <a name="connectors"></a>커넥터 ##

워크플로는 커넥터를 사용하여 검토 도구와 통신함으로써 기능을 추가합니다. 검토 도구는 콘텐츠를 조정하는 기본 워크플로가 포함된 Content Moderator API를 호출합니다. 검토 도구에 등록하면 Moderator API 자격 증명이 자동으로 프로비전됩니다. 또한 커넥터를 사용할 수 있는 한, 다른 커넥터 API 통합을 지원합니다. 즉시 사용할 수 있는 몇 가지 커넥터가 준비되어 있습니다.

[커넥터 탭](connectors.md)에서는 커넥터를 관리할 수 있습니다. 커넥터를 추가 또는 삭제하고, 특정 커넥터에 대한 구독 키를 찾을 수 있습니다. [연결]을 클릭하면 사용자 지정 워크플로에 커넥터를 추가할 수 있습니다. 

![Content Moderator 커넥터 설정](images/settings-4-connectors.png)

## <a name="workflows"></a>워크플로 ##

[워크플로] 탭에서는 워크플로를 관리할 수 있습니다. 샘플 파일을 업로드하여 워크플로 테스트할 수 있습니다. 또한 제공되는 API 커넥터([커넥터] 탭에서 찾을 수 있음)를 사용하여 이미지 및 텍스트에 대한 [사용자 지정 워크플로를 정의](workflows.md)할 수 있습니다. 

![Content Moderator 워크플로 설정](images/settings-5-workflows.png)

## <a name="credentials"></a>자격 증명 ##

이 탭에서는 Content Moderator와 함께 제공되는 API(Image Moderation, Text Moderation, List Management, Workflow 및 Review API)를 사용하려면 필요한 Content Moderator 구독 키에 신속하게 액세스할 수 있습니다.
 
![Content Moderator 자격 증명](images/settings-6-credentials.png)
