---
title: Azure Data Factory를 사용 하 여 LastModifiedDate에서 새로운 기능과 변경 된 파일 복사 | Microsoft Docs
description: 솔루션 템플릿을 사용 하 여 Azure Data Factory를 사용 하 여 LastModifiedDate에서 새로운 기능과 변경 된 파일을 복사 하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312816"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Azure Data Factory를 사용 하 여 LastModifiedDate에서 새로운 기능과 변경 된 파일 복사

이 문서에서는 LastModifiedDate 해야만 새 파일과 변경 된 파일 기반 저장소에서 대상 저장소로 복사 하는 데 사용할 수 있는 솔루션 템플릿을 설명 합니다. 

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 해당 특성에 의해서만 새로운 기능과 변경 된 파일을 먼저 선택 **LastModifiedDate**, 다음 대상 데이터 저장소에 데이터 원본 저장소에서 해당 선택한 파일을 복사 합니다.

템플릿은 하나의 활동을 포함 합니다.
- **복사** 복사할 LastModifiedDate 해야만 새 파일과 변경 된 파일 저장소에서 대상 저장소입니다.

템플릿 매개 변수 4 개를 정의합니다.
-  *FolderPath_Source* 원본 저장소에서 파일을 읽을 수 있는 폴더 경로입니다. 기본 값을 고유한 폴더 경로로 대체 해야 합니다.
-  *FolderPath_Destination* 대상 저장소에 파일을 복사 하려는 폴더 경로입니다. 기본 값을 고유한 폴더 경로로 대체 해야 합니다.
-  *LastModified_From* LastModifiedDate 특성이 후 해당 파일을 선택 하는 데 사용 되었거나이 날짜/시간 값과 동일 합니다.  새 파일에만 복사 되지 않은 마지막 시간을 선택 하려면 날짜/시간 값이 경우 파이프라인을 마지막으로 트리거된 시간을 수 있습니다. 기본값 ' 2019를 바꿀 수 있습니다-02-01T00:00:00Z' UTC 표준 시간대에서에 예상된 LastModifiedDate 하 합니다. 
-  *LastModified_To* LastModifiedDate 특성이 날짜/시간 값이 하기 전에 해당 파일을 선택 하는 데 사용 됩니다. 새 파일에만 복사 되지 않은 마지막 시간을 선택 하려면 날짜/시간 값이 현재 시간을 수 있습니다.  기본값 ' 2019를 바꿀 수 있습니다-02-01T00:00:00Z' UTC 표준 시간대에서에 예상된 LastModifiedDate 하 합니다. 

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 템플릿을 이동 **LastModifiedDate 해야만 새 파일을 복사**합니다. 만들기는 **새로 만들기** 원본 storage 저장소에 연결 합니다. 원본 저장소 저장소에서 파일을 복사 하려는 경우

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 먼저 저장소 선택 **형식**합니다. 저장소 입력 한 후 **계정 이름** 하며 **계정 키**합니다. 마지막으로 선택 **완료**합니다.

    ![연결 문자열을 입력 합니다.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. 만들기는 **새로 만들기** 대상 저장소에 연결 합니다. 대상 저장소에 파일을 복사 하려는 경우 또한 단계 2에서으로 유사한 데이터 대상 저장소의 연결 정보를 입력 해야 합니다.

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. 선택 **이 템플릿을 사용 하 여**입니다.

    ![이 템플릿 사용](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

    ![파이프라인 표시](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. 선택 **디버그**에 대 한 값을 쓸 합니다 **매개 변수** 선택한 **마침**합니다.  아래 그림에서 다음과 같이 매개 변수를 설정 합니다.
   - **FolderPath_Source** =  **/source/**
   - **FolderPath_Destination** =  **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     예제는 사이의 시간 범위 내에서 마지막으로 수정 된 파일을 나타내는 *2019-02-01T00:00:00Z* 하 고 *2019-03-01T00:00:00Z* 폴더에서 복사할 */source/*  폴더로 */destination/* 합니다.  사용자 고유의 매개 변수를 사용 하 여이 대체할 수 있습니다.
    
     ![파이프라인 실행](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. 결과를 검토합니다. 구성 된 내에 마지막으로 수정 된 파일만 표시 됩니다 timespan 대상 저장소에 복사 되었습니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. 이제 파이프라인 LastModifiedDate에 의해서만 새로운 기능과 변경 된 파일을 주기적으로 복사 항상 수 있도록이 파이프라인을 자동화 하는 연속 windows 트리거를 추가할 수 있습니다.  선택 **트리거 추가**, 선택한 **새로 만들기/편집**합니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. **트리거 추가** 창에서 **+ 새로 만들기**를 선택합니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. 선택 **Tumbling Window** 트리거 형식에 대 한 설정 **15 분 마다** (모든 간격 시간을 변경할 수), 되풀이를 선택한 후 **다음**합니다.

    ![트리거 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. 에 대 한 값을 쓸 합니다 **트리거 실행 매개 변수** 으로 선택한 다음 **완료**합니다.
    - **FolderPath_Source** =  **/source/** .  원본 데이터 저장소의 폴더를 사용 하 여 바꿀 수 있습니다.
    - **FolderPath_Destination** =  **/destination/** .  대상 데이터 저장소의 폴더를 사용 하 여 바꿀 수 있습니다.
    - **LastModified_From** =   **@trigger().outputs.windowStartTime**.  트리거의 경우 파이프라인을 마지막으로 트리거된 시간을 결정 하는 시스템 변수는 것입니다.
    - **LastModified_To** =  **@trigger().outputs.windowEndTime**.  파이프라인이 이번 트리거될 때 시간을 결정 하는 트리거에서 시스템 변수로 것입니다.
    
    ![입력 매개 변수](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. **모두 게시**를 선택합니다.
    
    ![모두 게시](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. 데이터 원본 저장소의 소스 폴더에 새 파일을 만듭니다.  이제 자동으로 트리거될 파이프라인에 대 한 대기 중인 하 고 새 파일에만 대상 저장소에 복사 됩니다.

14. 선택 **모니터링** 왼쪽된 탐색 창에서 탭 및 트리거의 되풀이 15 분 마다로 설정한 경우 약 15 분 동안 대기 합니다. 

    ![모니터링을 선택 합니다.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. 결과를 검토합니다. 파이프라인에 자동으로 매 15 분 마다 트리거되고 각 파이프라인 실행의 대상 저장소로 원본 저장소에서 새롭거나 변경 된 파일을 복사할 표시 됩니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)