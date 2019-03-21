---
title: 콘텐츠를 조정하면서 다른 서비스에 연결 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 커넥터를 사용하여 Content Moderator 워크플로에 대한 다른 API에 액세스하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3ee582e2541e4eb55e5ea1424782df132ecf3575
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116557"
---
# <a name="connect-to-other-cognitive-services"></a>다른 Cognitive Services에 연결

Azure Content Moderator 워크플로는 Content Moderator API 외에도 다른 API를 사용할 수 있습니다. Content Moderator에서 커넥터를 사용하여 다른 API에 액세스합니다. 커넥터는 다른 API에 대한 링크를 제공합니다.

Content Moderator는 다음과 같은 기본 커넥터를 포함하고 있습니다.

* Emotion API
* Face API
* PhotoDNA 클라우드 서비스

![Content Moderator에서 제공하는 커넥터](images/connectors-1.png)

## <a name="verify-your-credentials"></a>자격 증명 확인 

워크플로를 정의하기 전에, 사용하려는 커넥터 API에 대한 유효한 자격 증명이 있는지 확인합니다.

1. 검토 도구 대시보드에서 **설정** > **커넥터**를 선택합니다.

   ![Content Moderator 커넥터 선택](images/connectors-2.png)

2. 자격 증명을 확인할 커넥터 옆에 있는 **편집** 기호를 선택합니다.

   ![Content Moderator 편집 기호 선택](images/connectors-3.png)

3. 구독 키가 나타납니다. 편집을 마친 후 **저장**을 선택합니다.

   ![Content Moderator 커넥터 편집 페이지](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>커넥터 추가

1. 커넥터를 추가하려면 구독 키가 필요합니다. 검토 도구 대시보드에서 **설정** > **자격 증명**을 선택합니다. **Ocp-Admin-Subscription-Key** 상자의 값을 선택 및 복사합니다.

2. **커넥터**를 선택합니다. 검토 도구 대시보드에 표시된 사용 가능한 커넥터 중 하나를 선택합니다. 그런 다음, **연결**을 선택합니다. 

   ![Content Moderator 커넥터 추가 페이지](images/connectors-5.png)

3. 앞에서 복사한 키를 **Ocp-Admin-Subscription-Key** 상자에 붙여넣습니다. 그런 다음 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* 커넥터를 사용하여 [사용자 지정 워크플로를 정의](workflows.md)하는 방법을 알아봅니다.
