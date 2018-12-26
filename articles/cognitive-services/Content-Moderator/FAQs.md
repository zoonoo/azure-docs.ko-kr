---
title: 질문과 대답 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator에 대해 자주 묻는 질문과 대답입니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: faq
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: ce61a89069caf90695a2cb6c54f61de8fa8cb112
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226936"
---
# <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

#### <a name="what-does-my-content-moderator-subscription-include"></a>Content Moderator 구독에 무엇이 포함됩니까?
Content Moderator 구독에는 검토 도구 및 API에 대한 액세스 권한이 포함됩니다. 비즈니스 요구 사항에 따라 둘 중 무엇을 사용할 것인지, 또는 둘 다 사용할 것인지 결정할 수 있습니다.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>API를 사용하여 조정할 수 있는 콘텐츠의 한도/제한 사항은 무엇인가요?
API를 사용할 경우 이미지 크기는 최소 128픽셀, 최대 파일 크기는 4MB입니다. 텍스트는 1024자까지 허용됩니다. 비디오 시간에는 제한이 없습니다.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>텍스트 API 또는 이미지 API로 전달된 콘텐츠 크기가 한도를 초과하면 어떻게 되나요?
텍스트 API는 텍스트가 허용된 길이를 초과한다고 알리는 오류 코드를 반환합니다. 이미지 API는 이미지가 크기 요구 사항에 맞지 않는다고 알리는 오류 코드를 반환합니다.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>조정을 위해 제출된 이미지, 텍스트 또는 비디오를 Microsoft에서 보관하나요?
제출된 콘텐츠의 소유권은 제출자에게 있으며 Microsoft는 소유자의 동의 없이 콘텐츠를 보관하지 않습니다. Microsoft는 업계 최고 수준의 보안 조치를 사용하여 콘텐츠를 보호합니다.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Content Moderator를 사용하여 불법 아동 착취물 이미지를 차단할 수 있나요?
아니요. 그러나 인증된 조직에서 [PhotoDNA 클라우드 서비스](https://www.microsoft.com/photodna "Microsoft PhotoDNA 클라우드 서비스")를 사용하여 이러한 종류의 콘텐츠를 차단할 수 있습니다.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>사용자는 최대 몇 개의 검토 팀에 조인할 수 있나요? 사용자가 팀을 바꿀 수 있나요?
사용자는 한 번에 한 팀에만 조인할 수 있습니다.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>검토 도구에서 지원되는 팀 멤버 역할의 종류는 무엇입니까? 어떻게 다를까요?
현재 Studio에서는 관리자 및 검토자 역할만 허용됩니다. 관리자는 다른 사용자를 초대하고 구성 설정에 대한 액세스 권한을 가질 수 있지만, 검토자는 조정 결과를 검토하고 콘텐츠에 태그를 지정하거나 지정 해제하는 것만 가능합니다.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>태그란? 검토 도구에서 사용자 지정 태그를 지원합니까?
태그는 adult를 나타내는 'a', racy를 나타내는 'r' 같은 조정 플래그를 나타내는 1자 또는 2자로 된 짧은 코드입니다. 관리자는 필요에 따라 비즈니스에 대한 새 태그를 정의할 수 있습니다.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>검토 팀 하나에 배치 가능한 최대 팀 멤버 수는 몇 명입니까?
한 팀에는 관리자 한 명을 포함하여 최대 5명의 팀원을 배치할 수 있습니다.
