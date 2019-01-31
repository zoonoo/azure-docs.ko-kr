---
title: 사용자 지정 목록 및 API 콘솔을 사용하여 이미지 조정 - Content Moderator
titlesuffix: Azure Content Moderator
description: Azure Content Moderator의 목록 관리 API를 사용하여 사용자 지정 이미지 목록을 만듭니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 727c12d50ab9233fadc6f87d14da1eaa46e8ed35
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217764"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>API 콘솔에서 사용자 지정 이미지 목록을 사용하여 조정

Azure Content Moderator에서 [목록 관리 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)를 사용하여 사용자 지정 이미지 목록을 만듭니다. 이미지 조정 API와 함께 사용자 지정 이미지 목록을 사용합니다. 이미지 조정 작업이 이미지를 평가합니다. 사용자 지정 목록을 만드는 경우 작업은 사용자 지정 목록의 이미지와 비교합니다. 사용자 지정 목록을 사용하여 이미지를 차단하거나 허용할 수 있습니다.

> [!NOTE]
> 최대 **5개 이미지 목록**으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.
>

목록 관리 API를 사용하여 다음 작업을 수행합니다.

- 목록을 만듭니다.
- 목록에 이미지를 추가합니다.
- 목록의 이미지에 대해 이미지를 차단합니다.
- 목록에서 이미지를 삭제합니다.
- 목록을 삭제합니다.
- 목록 정보를 편집합니다.
- 목록에 대한 변경 내용이 새 검색에 포함되도록 인덱스를 새로 고칩니다.

## <a name="use-the-api-console"></a>API 콘솔 사용
온라인 콘솔에서 API를 테스트하려면 구독 키가 필요합니다. 구독 키는 **Ocp-Apim-Subscription-Key** 상자의 **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

## <a name="refresh-search-index"></a>검색 인덱스 새로 고침

이미지 목록을 변경한 후 이후 검색에 포함되도록 변경 내용에 대한 해당 인덱스를 새로 고쳐야 합니다. 이 단계는 데스크톱의 검색 엔진(활성화된 경우) 또는 웹 검색 엔진이 새 파일 또는 페이지를 포함하도록 해당 인덱스를 지속적으로 새로 고치는 방법과 유사합니다.

1.  [이미지 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)의 왼쪽 메뉴에서 **이미지 목록**, **검색 인덱스 새로 고침**을 차례로 선택합니다.

  **이미지 목록 - 검색 인덱스 새로 고침** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 
 
    ![이미지 목록 - 검색 인덱스 새로 고침 페이지 지역 선택](images/test-drive-region.png)

    **이미지 목록 - 검색 인덱스 새로 고침** API 콘솔이 열립니다.

3.  **listId** 상자에 목록 ID를 입력합니다. 구독 키를 입력한 다음, **보내기**를 선택합니다.

  ![이미지 목록 - 검색 인덱스 새로 고침 콘솔 응답 콘텐츠 상자](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>이미지 목록 만들기

1.  [이미지 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)로 이동합니다.

  **이미지 목록 - 만들기** 페이지가 열립니다. 

3.  **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.

    ![이미지 목록 - 만들기 페이지 영역 선택](images/test-drive-region.png)

    **이미지 목록 - 만들기** API 콘솔이 열립니다.
 
4.  **Ocp-Apim-Subscription-Key** 상자에 구독 키를 입력합니다.

5.  **요청 본문** 상자에 **이름**(예: MyList) 및 **설명**에 대한 값을 입력합니다.

  ![이미지 목록 - 콘솔 요청 본문 이름 및 설명 만들기](images/try-terms-list-create-1.png)

6.  키-값 쌍 자리 표시자를 사용하여 목록에 보다 설명적인 메타데이터를 지정합니다.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  키-값 쌍으로 실제 이미지가 아닌 목록 메타데이터를 추가합니다.
 
7.  **보내기**를 선택합니다. 목록이 생성됩니다. 새 목록과 연결된 **ID**입니다. 다른 이미지 목록 관리 기능에 이 ID가 필요합니다.

  ![이미지 목록 - 만들기 콘솔 응답 콘텐츠 상자는 목록 ID를 표시합니다.](images/try-terms-list-create-2.png)
 
8.  다음으로 MyList에 이미지를 추가합니다. 왼쪽 메뉴에서 **이미지**를 선택한 다음, **이미지 추가**를 선택합니다.

  **이미지 - 이미지 추가** 페이지가 열립니다. 

9. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.

  ![이미지 - 이미지 추가 페이지 영역 선택](images/test-drive-region.png)

  **이미지 - 이미지 추가** API 콘솔이 열립니다.
 
10. **listId** 상자에 생성한 목록 ID를 입력한 다음, 추가하려는 이미지의 URL을 입력합니다. 구독 키를 입력한 다음, **보내기**를 선택합니다.

11. 목록에 이미지가 추가되었는지 확인하려면 왼쪽 메뉴에서 **이미지**를 선택한 다음, **모든 이미지 ID 가져오기**를 선택합니다.

  **이미지 - 모든 이미지 ID 가져오기** API 콘솔이 열립니다.
  
12. **listId** 상자에 목록 ID를 입력한 다음, 등록 키를 입력합니다. **보내기**를 선택합니다.

  ![이미지 - 모든 이미지 ID 가져오기 콘솔 응답 콘텐츠 상자는 입력한 이미지를 나열합니다.](images/try-image-list-create-11.png)
 
10. 몇 가지 이미지를 추가합니다. 이제 사용자 지정 이미지 목록을 만들었으므로 사용자 지정 이미지 목록을 사용하여 [이미지 평가](try-image-api.md)를 시도합니다. 

## <a name="delete-images-and-lists"></a>이미지 및 목록 삭제

이미지 또는 목록을 삭제하는 것은 간단합니다. API를 사용하여 다음 작업을 수행할 수 있습니다.

- 이미지 삭제를 삭제합니다. (**이미지 - 삭제**)
- 목록을 삭제하지 않고 목록의 모든 이미지를 삭제합니다. (**이미지 - 모든 이미지 삭제**)
- 목록 및 모든 해당 콘텐츠를 삭제합니다. (**이미지 목록 - 삭제**)

이 예제에서는 단일 이미지를 삭제합니다.

1. [이미지 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)의 왼쪽 메뉴에서 **이미지**, **삭제**를 차례로 선택합니다. 

  **이미지 - 삭제** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

  ![이미지 - 삭제 페이지 영역 선택](images/test-drive-region.png)
 
  **이미지 - 삭제** API 콘솔이 열립니다.
 
3.  **listId** 상자에 이미지를 삭제할 목록의 ID를 입력합니다.  MyList에 대한 **이미지 - 모든 이미지 ID 가져오기** 콘솔에서 반환되는 번호입니다. 그런 다음, 삭제할 이미지의 **ImageId**를 입력합니다. 

예제에서 목록 ID는 **ContentSource**에 대한 값, **58953**입니다. 이미지 ID는 **ContentIds**에 대한 값, **59021**입니다.

4.  구독 키를 입력한 다음, **보내기**를 선택합니다.

5.  이미지가 삭제되었는지 확인하려면 **이미지 - 모든 이미지 ID 가져오기** 콘솔을 사용합니다.
 
## <a name="change-list-information"></a>목록 정보 변경

목록의 이름 및 설명을 편집하고, 메타데이터 항목을 추가할 수 있습니다.

1.  [이미지 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)의 왼쪽 메뉴에서 **이미지 목록**, **세부 정보 업데이트**를 차례로 선택합니다. 

  **이미지 목록 -세부 정보 업데이트** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.  

    ![이미지 목록 - 세부 정보 업데이트 페이지 영역 선택](images/test-drive-region.png)

    **이미지 목록 -세부 정보 업데이트** API 콘솔이 열립니다.
 
3.  **listId** 상자에 목록 ID를 입력한 다음, 등록 키를 입력합니다.

4.  **요청 본문** 상자에서 편집을 수행한 다음, 페이지에서 **보내기** 단추를 선택합니다.

  ![이미지 목록 - 세부 정보 업데이트 콘솔 요청 본문 편집](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용하거나 [이미지 목록 .NET 빠른 시작](image-lists-quickstart-dotnet.md)을 시작하여 애플리케이션을 통합합니다.
