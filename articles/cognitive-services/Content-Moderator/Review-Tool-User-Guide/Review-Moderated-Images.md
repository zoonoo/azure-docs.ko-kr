---
title: Azure Content Moderator에서 이미지 검토 | Microsoft Docs
description: 검토 도구를 통해 중재자가 이미지를 검토하도록 할 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/06/2017
ms.author: sajagtap
ms.openlocfilehash: 655c4b6d142c9f792a3b16c6b20d53efef51ea5b
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852829"
---
# <a name="review-moderated-images"></a>조정된 이미지 검토

콘텐츠 조정에 가입하고 구독 키를 얻은 후에는 이미지 검토 기능을 사용해 볼 수 있습니다.

1.  [사용자 검토 도구](https://contentmoderator.cognitive.microsoft.com/)를 열고 로그인합니다. 
2.  [Try(시도)] 탭을 클릭하고 검토할 일부 이미지를 업로드합니다.
3.  [검토] 탭을 클릭하고 [이미지]를 선택합니다.

  ![검토 이미지 옵션이 강조 표시된 검토 도구를 보여주는 Chrome 브라우저](images/review-images-1.png)

  이미지는 검토 도구에서 할당한 레이블과 함께 표시됩니다. 이미지를 검토하는 동안 팀의 다른 검토자가 해당 이미지를 사용할 수 없습니다.

4.  화면에 표시되는 이미지 수를 조정하려면 “표시할 검토” 슬라이더(1)를 이동합니다. 태그가 지정되었거나 지정되지 않은 단추(2)를 클릭하여 이미지를 적절히 정렬합니다. 태그(3)를 클릭하여 설정 또는 해제합니다.

  ![검토에 대한 태그가 지정된 이미지를 사용하는 검토 도구를 보여주는 Chrome 브라우저](images/review-images-2.png)
 
5.  이미지에 대한 자세한 정보를 보려면 미리 보기의 줄임표를 클릭하고 **세부 정보 보기** 옵션을 클릭합니다. 이미지를 하위 팀에 할당하려면 **다음으로 이동** 옵션을 선택합니다.
 
  ![보기 세부 정보 옵션이 강조 표시된 이미지](images/review-images-3.png)

6. 세부 정보 페이지에서 이미지 조정 정보를 찾아봅니다.

  ![별도 창에서 나열된 조정 세부 정보를 사용하는 이미지](images/review-images-4.png)
 
7.  태그 할당을 검토하여 필요에 따라 업데이트한 후에는 **다음**을 클릭하여 검토를 제출합니다.

제출 후에는 약 5초 이내에 **이전** 단추를 클릭하여 이전 화면으로 돌아가 이미지를 다시 검토합니다. 그 후에는 이미지가 제출 큐에 더 이상 없으며 **이전** 단추를 더 이상 사용할 수 없습니다.
