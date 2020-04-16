---
title: LastModifiedDate로 새 파일 및 변경된 파일 복사
description: 솔루션 템플릿을 사용하여 Azure 데이터 팩터리에서 LastModifiedDate에서 새 파일과 변경된 파일을 복사하는 방법을 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 979977b2dd2eb5742d4e488623c79cb91427f055
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414809"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Azure 데이터 팩터리에서 LastModifiedDate로 새 파일 및 변경된 파일 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 파일 기반 저장소에서 대상 저장소에 LastModifiedDate에서만 새 파일및 변경된 파일을 복사하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명합니다. 

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 먼저 **LastModifiedDate**특성에 의해서만 새 파일과 변경된 파일을 선택한 다음 데이터 원본 저장소에서 데이터 대상 저장소로 선택한 파일을 복사합니다.

템플릿에는 다음 한 가지 활동이 포함되어 있습니다.
- **파일** 저장소에서 대상 저장소로 LastModifiedDate에서만 새 파일과 변경된 파일을 복사합니다.

템플릿은 6개의 매개 변수를 정의합니다.
-  *FolderPath_Source* 소스 저장소에서 파일을 읽을 수 있는 폴더 경로입니다. 기본값을 자신의 폴더 경로로 바꿔야 합니다.
-  *Directory_Source* 소스 저장소에서 파일을 읽을 수 있는 하위 폴더 경로입니다. 기본값을 사용자 고유의 하위 폴더 경로로 바꿔야 합니다.
-  *FolderPath_Destination* 대상 저장소에 파일을 복사하려는 폴더 경로입니다. 기본값을 자신의 폴더 경로로 바꿔야 합니다.
-  *Directory_Destination* 대상 저장소에 파일을 복사하려는 하위 폴더 경로입니다. 기본값을 사용자 고유의 하위 폴더 경로로 바꿔야 합니다.
-  *LastModified_From* LastModifiedDate 특성이 이 datetime 값 이후이거나 같을 파일을 선택하는 데 사용됩니다.  마지막으로 복사되지 않은 새 파일만 선택하려면 이 datetime 값은 파이프라인이 마지막으로 트리거된 시간일 수 있습니다. 기본값 '2019-02-01T00:00:00Z'를 UTC 표준 시간대의 예상 LastModifiedDate로 바꿀 수 있습니다. 
-  *LastModified_To* LastModifiedDate 특성이 이 datetime 값 앞에 있는 파일을 선택하는 데 사용됩니다. 마지막으로 복사되지 않은 새 파일만 선택하려면 이 datetime 값이 현재 시간이 될 수 있습니다.  기본값 '2019-02-01T00:00:00Z'를 UTC 표준 시간대의 예상 LastModifiedDate로 바꿀 수 있습니다. 

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 템플릿으로 이동 **LastModifiedDate 에서만 새 파일 복사**. 원본 저장소 저장소에 대한 **새** 연결을 만듭니다. 원본 저장소 저장소에서 파일을 복사하려는 위치입니다.

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 대상 저장소에 대한 **새** 연결을 만듭니다. 대상 저장소는 파일을 복사할 위치입니다. 

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. **이 템플릿 사용을**선택합니다.

    ![이 템플릿 사용](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

    ![파이프라인 표시](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. **디버그를**선택하고 **매개 변수에** 대한 값을 작성하고 **완료를**선택합니다.  아래 그림에서 매개 변수를 다음과 같이 설정합니다.
   - **FolderPath_Source** = 소스 폴더
   - **Directory_Source** = 하위 폴더
   - **FolderPath_Destination** = 대상 폴더
   - **Directory_Destination** = 하위 폴더
   - **LastModified_From** = 2019-02-01T00:00Z
   - **LastModified_To** = 2019-03-01T00:00Z
    
    이 예는 시간 범위 내에 마지막으로 수정된**파일(2019-02-01T00:00:00Z** ~ **2019-03-01T00:00:00Z)이**소스 경로 **소스 폴더/하위 폴더에서** 대상 경로 **폴더/하위 폴더로**복사되었음을 나타냅니다.  이러한 매개 변수를 사용자 고유의 매개 변수로 바꿀 수 있습니다.

    ![파이프라인 실행](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 결과를 검토합니다. 구성된 시간 범위 내에 마지막으로 수정된 파일만 대상 저장소에 복사된 것을 볼 수 있습니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. 이제 이 파이프라인을 자동화하기 위해 텀블링 윈도우 트리거를 추가하여 파이프라인이 항상 LastModifiedDate에서만 새 파일을 복사할 수 있도록 할 수 있습니다.  **트리거 추가를**선택하고 **새로/편집을**선택합니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. **트리거 추가** 창에서 **+ 새로 만들기**를 선택합니다.

9. 트리거 유형에 대해 **텀블링 창을** 선택하고 **15분마다** 되풀이로 설정합니다(간격 시간으로 변경할 수 있음). 활성화된 상자에 **대해 예를** 선택한 다음 **확인을**선택합니다.

    ![트리거 만들기](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. **트리거 실행 매개 변수의** 값을 다음과 같이 설정하고 **완료를**선택합니다.
    - **FolderPath_Source** = **소스 폴더**.  원본 데이터 저장소에서 폴더로 바꿀 수 있습니다.
    - **Directory_Source** = **하위 폴더**.  원본 데이터 저장소에서 하위 폴더로 바꿀 수 있습니다.
    - **FolderPath_Destination** = **대상 폴더**.  대상 데이터 저장소에서 폴더로 바꿀 수 있습니다.
    - **Directory_Destination** = **하위 폴더**.  대상 데이터 저장소에서 하위 폴더로 바꿀 수 있습니다.
    - **LastModified_From** =  **LastModified_From\@트리거().outputs.windowStartTime**.  파이프라인이 마지막으로 트리거된 시간을 결정하는 트리거의 시스템 변수입니다.
    - **LastModified_To** = **LastModified_To\@트리거().outputs.windowEndTime**.  이번에는 파이프라인이 트리거되는 시간을 결정하는 트리거의 시스템 변수입니다.
    
    ![입력 매개 변수](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. **모두 게시**를 선택합니다.
    
    ![모두 게시](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. 데이터 원본 저장소의 원본 폴더에 새 파일을 만듭니다.  이제 파이프라인이 자동으로 트리거되고 새 파일만 대상 저장소에 복사됩니다.

13. 왼쪽 탐색 패널에서 **모니터** 탭을 선택하고 트리거의 재발이 15분마다 설정된 경우 약 15분 동안 기다립니다. 

14. 결과를 검토합니다. 파이프라인이 15분마다 자동으로 트리거되고 원본 저장소의 새 파일이나 변경된 파일만 각 파이프라인 실행의 대상 저장소에 복사됩니다.

    ![결과 검토](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
