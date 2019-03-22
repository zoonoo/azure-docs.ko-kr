---
title: 콘텐츠 조정에 사용자 지정 태그 사용 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator에는 기본 태그가 포함되어 있으며, 비즈니스와 관련된 콘텐츠를 조정하는 데 사용할 사용자 지정 태그를 만들 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: fa3b7be6ac7cc398685bf5ad7d21173bcb072b69
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118411"
---
# <a name="create-and-use-moderation-tags"></a>조정 태그 만들기 및 사용

두 개의 기본 태그 **isadult** (**a**) 및 **isracy** (**r**) 외에도, 검사 대상을 보다 구체적으로 지정하기 위한 사용자 지정 태그를 만들 수 있습니다. 이러한 사용자 지정 태그는 사용자 검토자가 이미지 또는 텍스트를 할당하는 데 사용할 수 있습니다.

## <a name="create-tags"></a>태그 만들기

1. [설정] 탭에서 태그를 선택합니다.

   ![Content Moderation 태그](images/tags-1.png)

2. 태그에 대한 두 글자 길이의 짧은 코드를 입력합니다.
3. 태그 이름을 입력합니다. 이름은 짧고 구체적이어야 합니다. 예를 들면 **isbullying**입니다.
4. 설명을 입력합니다.
5. 추가를 클릭합니다.
6. 태그 만들기가 끝났으면 [저장]을 클릭합니다.

![Content Moderation 태그 정의](images/tags-2-define.png)

## <a name="using-custom-tags"></a>사용자 지정 태그 사용

사용자 지정 태그는 사용자 검토에 사용됩니다. 미리 보기에 표시되며, 검토자는 사용자 지정 태그를 클릭하여 선택할 수 있습니다.

![Content Moderation 태그 사용](images/tags-3-use.png)

[표시됨]을 선택하거나 선택 취소하여 여러 검토의 여러 태그를 해제할 수 있습니다.
 
![Content Moderation 태그 사용 안 함](images/tags-4-disable.png)

두 기본 태그 **isadult** 및 **isracy**는 삭제할 수는 없지만, 사용자가 정의한 사용자 지정 태그는 삭제할 수 있습니다. 삭제할 태그 옆에 있는 쓰레기통을 클릭하면 됩니다.

![Content Moderation 태그 삭제](images/tags-5-delete.png)

## <a name="next-steps"></a>다음 단계

태그를 이미지 조정에 사용하는 방법을 알아보려면 [조정된 이미지 검토](Review-Moderated-Images.md)를 참조하세요.
