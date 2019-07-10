---
title: 사용자 지정 용어 목록을 사용하여 텍스트 조정 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 목록 관리 API를 사용하여 텍스트 조정 API와 함께 사용할 사용자 지정 용어 목록을 만드는 방법을 설명합니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 28029fe92a207dba85e2ab5a22c08879b7172925
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097949"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>API 콘솔에서 사용자 지정 용어 목록을 사용하여 조정

Azure Content Moderator에서 기본 전역 용어 목록은 대부분의 콘텐츠 조정 요구에 적합합니다. 그러나 조직에 관련된 용어에 대해 차단해야 할 수 있습니다. 예를 들어 추가 검토를 위해 경쟁 업체 이름에 태그를 지정할 수 있습니다. 

[목록 관리 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)를 사용하여 텍스트 조정 API와 함께 사용할 사용자 지정 용어 목록을 만듭니다. **텍스트 - 차단** 작업은 텍스트에서 욕설을 검색하고 사용자 지정 및 공유 블랙리스트에 대해 텍스트를 비교합니다.

> [!NOTE]
> 최대 **5개 용어 목록**으로 제한되고, 각 목록이 **10,000개 용어를 초과하지 않아야** 합니다.
>

목록 관리 API를 사용하여 다음 작업을 수행할 수 있습니다.
- 목록을 만듭니다.
- 목록에 용어를 추가합니다.
- 목록의 용어에 대해 용어를 차단합니다.
- 목록에서 용어를 삭제합니다.
- 목록을 삭제합니다.
- 목록 정보를 편집합니다.
- 목록에 대한 변경 내용이 새 검색에 포함되도록 인덱스를 새로 고칩니다.

## <a name="use-the-api-console"></a>API 콘솔 사용

온라인 콘솔에서 API를 시험 사용할 수 있으려면 먼저 구독 키가 필요합니다. 이 키는 **Ocp-Apim-Subscription-Key** 상자의 **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

## <a name="refresh-search-index"></a>검색 인덱스 새로 고침

용어 목록을 변경한 후 이후 검색에 포함되도록 변경 내용에 대한 해당 인덱스를 새로 고쳐야 합니다. 이 단계는 데스크톱의 검색 엔진(활성화된 경우) 또는 웹 검색 엔진이 새 파일 또는 페이지를 포함하도록 해당 인덱스를 지속적으로 새로 고치는 방법과 유사합니다.

1. [용어 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)의 왼쪽 메뉴에서 **용어 목록**, **검색 인덱스 새로 고침**을 차례로 선택합니다. 

   **용어 목록 - 검색 인덱스 새로 고침** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

   ![용어 목록 - 검색 인덱스 새로 고침 페이지 지역 선택](images/test-drive-region.png)

   **용어 목록 - 검색 인덱스 새로 고침** API 콘솔이 열립니다.

3. **listId** 상자에 목록 ID를 입력합니다. 구독 키를 입력한 다음, **보내기**를 선택합니다.

   ![용어 목록 API - 검색 인덱스 새로 고침 콘솔 응답 콘텐츠 상자](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>용어 목록 만들기
1. [용어 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)로 이동합니다. 

   **용어 목록 - 만들기** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

   ![용어 목록 - 만들기 페이지 영역 선택](images/test-drive-region.png)

   **용어 목록 - 만들기** API 콘솔이 열립니다.
 
3. **Ocp-Apim-Subscription-Key** 상자에 구독 키를 입력합니다.

4. **요청 본문** 상자에 **이름**(예: MyList) 및 **설명**에 대한 값을 입력합니다.

   ![용어 목록 - 콘솔 요청 본문 이름 및 설명 만들기](images/try-terms-list-create-1.png)

5. 키-값 쌍 자리 표시자를 사용하여 목록에 보다 설명적인 메타데이터를 지정합니다.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   키-값 쌍으로 실제 용어가 아닌 목록 메타데이터를 추가합니다.
 
6. **보내기**를 선택합니다. 목록이 생성됩니다. 새 목록과 연결된 **ID**입니다. 다른 용어 목록 관리 기능에 이 ID가 필요합니다.

   ![용어 목록 - 만들기 콘솔 응답 콘텐츠 상자는 목록 ID를 표시합니다.](images/try-terms-list-create-2.png)
 
7. MyList에 용어를 추가합니다. 왼쪽 메뉴의 **용어** 아래에서 **용어 추가**를 선택합니다. 

   **용어 - 용어 추가** 페이지가 열립니다. 

8. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

   ![용어 - 용어 추가 페이지 영역 선택](images/test-drive-region.png)

   **용어 - 용어 추가** API 콘솔이 열립니다.
 
9. **listId** 상자에 생성한 목록 ID를 입력하고, **언어**에 대한 값을 선택합니다. 구독 키를 입력한 다음, **보내기**를 선택합니다.

   ![용어 - 용어 추가 콘솔 쿼리 매개 변수](images/try-terms-list-create-3.png)
 
10. 목록에 용어가 추가되었는지 확인하려면 왼쪽 메뉴에서 **용어**를 선택한 다음, **모든 용어 가져오기**를 선택합니다. 

    **용어 - 모든 용어 가져오기** API 콘솔이 열립니다.

11. **listId** 상자에 목록 ID를 입력한 다음, 등록 키를 입력합니다. **보내기**를 선택합니다.

12. **응답 콘텐츠** 상자에서 입력한 용어를 확인합니다.

    ![용어 - 모든 용어 가져오기 콘솔 응답 콘텐츠 상자는 입력한 용어를 나열합니다.](images/try-terms-list-create-4.png)
 
13. 몇 가지 용어를 추가합니다. 이제 사용자 지정 용어 목록을 만들었으므로 사용자 지정 용어 목록을 사용하여 [일부 텍스트 검사](try-text-api.md)를 시도합니다. 

## <a name="delete-terms-and-lists"></a>용어 및 목록 삭제

용어 또는 목록을 삭제하는 것은 간단합니다. API를 사용하여 다음 작업을 수행합니다.

- 용어를 삭제합니다. (**용어 - 삭제**)
- 목록을 삭제하지 않고 목록의 모든 용어를 삭제합니다. (**용어 - 모든 용어 삭제**)
- 목록 및 모든 해당 콘텐츠를 삭제합니다. (**용어 목록 - 삭제**)

이 예제에서는 단일 용어를 삭제합니다.

1. [용어 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)의 왼쪽 메뉴에서 **용어**, **삭제**를 차례로 선택합니다. 

   **용어 - 삭제**가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

   ![용어 - 삭제 페이지 영역 선택](images/test-drive-region.png)

   **용어 - 삭제** API 콘솔이 열립니다.
  
3. **listId** 상자에 용어를 삭제하려는 목록의 ID를 입력합니다. 이 ID는 MyList에 대해 **용어 목록 - 세부 정보 가져오기** 콘솔에서 반환되는 번호입니다(예제에서 **122**). 용어를 입력하고 언어를 선택합니다.
 
   ![용어 - 삭제 콘솔 쿼리 매개 변수](images/try-terms-list-delete-1.png)

4. 구독 키를 입력한 다음, **보내기**를 선택합니다.

5. 용어가 삭제되었는지 확인하려면 **용어 목록 - 모두 가져오기** 콘솔을 사용합니다.

   ![용어 목록 - 모두 가져오기 콘솔 응답 콘텐츠 상자는 용어가 삭제되었음을 표시합니다.](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>목록 정보 변경

목록의 이름 및 설명을 편집하고, 메타데이터 항목을 추가할 수 있습니다.

1. [용어 목록 관리 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)의 왼쪽 메뉴에서 **용어 목록**, **세부 정보 업데이트**를 차례로 선택합니다. 

   **용어 목록 -세부 정보 업데이트** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

   ![용어 목록 - 세부 정보 업데이트 페이지 영역 선택](images/test-drive-region.png)

   **용어 목록 -세부 정보 업데이트** API 콘솔이 열립니다.

3. **listId** 상자에 목록 ID를 입력한 다음, 등록 키를 입력합니다.

4. **요청 본문** 상자에서 편집을 수행한 다음, **보내기**를 선택합니다.

   ![용어 목록 - 세부 정보 업데이트 콘솔 요청 본문 편집](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용하거나 [용어 목록 .NET 빠른 시작](term-lists-quickstart-dotnet.md)을 시작하여 애플리케이션을 통합합니다.
