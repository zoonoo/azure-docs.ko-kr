---
title: LUIS로 새 앱 만들기 | Microsoft Docs
description: LUIS(Language Understanding) 웹 페이지에서 응용 프로그램을 만들고 관리합니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 75edd39346995cdef72bb1e1fcb9eaff53d29702
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35382979"
---
# <a name="create-an-app"></a>앱 만들기
다음과 같은 다양한 방법으로 새 앱을 만듭니다. 

* 빈 앱으로 [시작](#create-new-app)하고 의도, 발화 및 엔터티를 만듭니다.
* 빈 앱으로 [시작](#create-new-app)하고 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 추가합니다.
* 이미 의도, 발화 및 엔터티가 포함된 JSON 파일에서 [LUIS 앱을 가져옵니다](#import-new-app).

## <a name="what-is-an-app"></a>앱이란?
앱에는 앱의 [협력자](luis-how-to-collaborate.md)와 모델의 [버전](luis-how-to-manage-versions.md)이 포함됩니다. 앱을 만들 때 **나중에 변경할 수 없는** 문화권([언어](luis-supported-languages.md))을 선택합니다. 

새 앱의 기본 버전은 “0.1”입니다. 

**내 앱** 페이지에서 응용 프로그램을 만들고 관리할 수 있습니다. [LUIS](luis-reference-regions.md) 웹 사이트의 맨 위 탐색 모음에서 **내 앱**을 선택하여 언제든지 이 페이지에 액세스할 수 있습니다. 

[![](media/luis-create-new-app/apps-list.png "앱 목록 스크린샷")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>새 앱 만들기

1. **내 앱** 페이지에서 **새 앱 만들기**를 선택합니다.
2. 대화 상자에서 응용 프로그램 이름을 “TravelAgent”로 지정합니다.

    ![새 앱 만들기 대화 상자](./media/luis-create-new-app/create-app.png)

3. 응용 프로그램 문화권을 선택한 다음(TravelAgent App의 경우 영어 선택), **완료**를 선택합니다. 

    >[!NOTE]
    >응용 프로그램을 만든 후에는 문화권을 변경할 수 없습니다. 

## <a name="import-new-app"></a>새 앱 가져오기
JSON 파일에서 앱의 이름(최대 50자), 버전(최대 10자) 및 설명을 설정할 수 있습니다. 응용 프로그램 JSON 파일의 예제는 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight)에서 사용할 수 있습니다.

1. **내 앱** 페이지에서 **새 앱 가져오기**를 선택합니다.
2. **새 앱 가져오기** 대화 상자에서 LUIS 앱을 정의하는 JSON 파일을 선택합니다.

    ![새 앱 가져오기 대화 상자](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>앱 내보내기
1. **내 앱** 페이지에서 앱 행 끝에 있는 세 개의 점(...)을 선택합니다.

    [![](media/luis-create-new-app/apps-list.png "앱별 작업의 팝업 대화 상자 스크린샷")](media/luis-create-new-app/three-dots.png#lightbox)

2. 메뉴에서 **앱 내보내기**를 선택합니다. 

## <a name="rename-app"></a>앱 이름 바꾸기

1. **내 앱** 페이지에서 앱 행 끝에 있는 세 개의 점(...)을 선택합니다. 
2. 메뉴에서 **이름 바꾸기**를 선택합니다.
3. 앱의 새 이름을 입력하고 **완료**를 선택합니다.

## <a name="delete-app"></a>앱 삭제

> [!CAUTION]
> 모든 협력자 및 소유자에 대한 앱을 삭제하려고 합니다. 삭제하기 전에 앱을 [내보냅니다](#export-app). 

1. **내 앱** 페이지에서 앱 행 끝에 있는 세 개의 점(...)을 선택합니다. 
2. 메뉴에서 **삭제**를 선택합니다.
3. 확인 창에서 **확인**을 선택합니다.

## <a name="export-endpoint-logs"></a>끝점 로그 내보내기
로그에는 쿼리, UTC 시간 및 LUIS JSON 응답이 포함됩니다.

1. **내 앱** 페이지에서 앱 행 끝에 있는 세 개의 점(...)을 선택합니다. 
2. 메뉴에서 **끝점 로그 내보내기**를 선택합니다.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>다음 단계

앱의 첫 번째 작업은 [의도를 추가](luis-how-to-add-intents.md)하는 것입니다.