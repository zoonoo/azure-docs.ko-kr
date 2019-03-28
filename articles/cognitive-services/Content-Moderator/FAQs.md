---
title: 질문과 대답 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator에 대해 자주 묻는 질문과 대답입니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351136"
---
# <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

### <a name="what-does-my-content-moderator-subscription-include"></a>Content Moderator 구독에 무엇이 포함됩니까?

구독의 Content Moderator 검토 도구 및 Api에 대 한 액세스를 포함합니다. 비즈니스 요구 사항에 따라 둘 중 무엇을 사용할 것인지, 또는 둘 다 사용할 것인지 결정할 수 있습니다.

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>API를 사용하여 조정할 수 있는 콘텐츠의 한도/제한 사항은 무엇인가요?

API를 사용할 경우 이미지 크기는 최소 128픽셀, 최대 파일 크기는 4MB입니다. 텍스트는 1024자까지 허용됩니다. 비디오 시간에는 제한이 없습니다.

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>텍스트 API 또는 이미지 API로 전달된 콘텐츠 크기가 한도를 초과하면 어떻게 되나요?

텍스트 API는 텍스트가 허용된 길이를 초과한다고 알리는 오류 코드를 반환합니다. 이미지 API는 이미지가 크기 요구 사항에 맞지 않는다고 알리는 오류 코드를 반환합니다.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>조정을 위해 제출된 이미지, 텍스트 또는 비디오를 Microsoft에서 보관하나요?

제출된 콘텐츠의 소유권은 제출자에게 있으며 Microsoft는 소유자의 동의 없이 콘텐츠를 보관하지 않습니다. Microsoft는 업계 최고 수준의 보안 조치를 사용하여 콘텐츠를 보호합니다.

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Content Moderator를 사용하여 불법 아동 착취물 이미지를 차단할 수 있나요?

아니요. 그러나 인증된 조직에서 [PhotoDNA 클라우드 서비스](https://www.microsoft.com/photodna "Microsoft PhotoDNA 클라우드 서비스")를 사용하여 이러한 종류의 콘텐츠를 차단할 수 있습니다.

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>개수 검토 팀 수 사용자 조인? 사용자가 팀을 바꿀 수 있나요?

사용자 한 번에 하나의 팀만 연결할 수 있습니다.

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>팀 멤버 역할의 종류는 검토 도구에서 지원 되나요? 어떻게 다를까요?

검토 도구는 현재 검토자 및 관리자 역할 할당을 허용 합니다. 관리자는 다른 사용자를 초대하고 구성 설정에 대한 액세스 권한을 가질 수 있지만, 검토자는 조정 결과를 검토하고 콘텐츠에 태그를 지정하거나 지정 해제하는 것만 가능합니다.

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>태그란? 검토 도구에서 사용자 지정 태그를 지원합니까?

태그는 외설 등에 대 한 성인을 'r'에 대 한 'a'와 같은 조정 플래그를 나타내는 하나 또는 두 문자 짧은 코드. 관리자는 필요에 따라 비즈니스에 대한 새 태그를 정의할 수 있습니다.

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>검토 팀 하나에 배치 가능한 최대 팀 멤버 수는 몇 명입니까?

팀에서 관리자를 포함 하 여 팀 멤버 5 명 까지를 할 수 있습니다.
