---
title: 파일 기반 저장소 간에 파일 이동
description: 솔루션 템플릿을 사용하여 Azure Data Factory를 사용하여 파일 기반 저장소 간에 파일을 이동하는 방법을 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b3165daa06ed975df9ccb677699d3ceb449327ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941936"
---
# <a name="move-files-with-azure-data-factory"></a>Azure 데이터 팩터리로 파일 이동

이 문서에서는 파일을 파일 기반 저장소 간에 한 폴더에서 다른 폴더로 이동하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명합니다. 이 템플릿을 사용하는 일반적인 시나리오 중 하나: 파일은 소스 저장소의 방문 폴더에 계속 삭제됩니다. ADF 파이프라인은 일정 트리거를 만들어 해당 파일을 원본에서 대상 저장소로 주기적으로 이동할 수 있습니다.  ADF 파이프라인이 "파일 이동"을 달성하는 방법은 방문 폴더에서 파일을 가져와 대상 저장소의 다른 폴더로 각각 복사한 다음 원본 저장소의 방문 폴더에서 동일한 파일을 삭제하는 것입니다.

> [!NOTE]
> 이 템플릿은 폴더를 이동하는 대신 파일을 이동하도록 설계되었습니다.  데이터 집합을 폴더 경로만 포함하도록 변경하여 폴더를 이동한 다음 복사 활동 및 삭제 활동을 사용하여 폴더를 나타내는 동일한 데이터 집합을 참조하려면 매우 신중해야 합니다. 복사 작업과 삭제 작업 중간에 새 파일이 폴더에 들어오지 않는지를 확인해야 하기 때문입니다. 복사 작업이 방금 복사 작업을 완료했지만 삭제 작업이 시작되지 않은 순간에 새 파일에 폴더에 도착하면 삭제 작업은 전체 폴더를 삭제하여 대상으로 아직 복사되지 않은 이러한 새로 도착한 파일을 삭제할 수 있습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 소스 파일 기반 저장소에서 파일을 가져옵니다. 그런 다음 각각을 대상 저장소로 이동합니다.

템플릿에는 다음 5가지 활동이 포함되어 있습니다.
- **GetMetadata** 소스 저장소에 폴더에서 파일 및 하위 폴더를 포함 하 여 개체의 목록을 가져옵니다. 개체를 재귀적으로 검색하지 않습니다. 
- **GetMetadata** 활동에서 개체 목록을 **필터링하여** 파일만 선택합니다. 
- **ForEach는** **필터** 활동에서 파일 목록을 가져옵니다.
- 원본에서 대상 저장소로 하나의 파일을 **복사합니다.**
- **삭제는** 원본 저장소에서 동일한 하나의 파일을 삭제합니다.

템플릿은 다음 네 가지 매개 변수를 정의합니다.
- *SourceStore_Location* 파일을 이동할 소스 저장소의 폴더 경로입니다. 
- *SourceStore_Directory* 파일을 이동하려는 소스 저장소의 하위 폴더 경로입니다.
- *DestinationStore_Location* 파일을 이동할 대상 저장소의 폴더 경로입니다. 
- *DestinationStore_Directory* 파일을 이동할 대상 저장소의 하위 폴더 경로입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일 이동** 템플릿으로 이동합니다. 기존 연결을 선택하거나 파일을 이동할 소스 파일 저장소에 **대한 새** 연결을 만듭니다. **DataSource_Folder** 및 **DataSource_File** 소스 파일 저장소의 동일한 연결을 참조합니다.

    ![원본에 대한 새 연결 만들기](media/solution-template-move-files/move-files1.png)

2. 기존 연결을 선택하거나 파일을 이동할 대상 파일 저장소에 **대한 새** 연결을 만듭니다.

    ![대상에 대한 새 연결 만들기](media/solution-template-move-files/move-files2.png)

3. **이 템플릿** 탭 사용을 선택합니다.
    
4. 다음 예제와 같이 파이프라인이 표시됩니다.

    ![파이프라인 표시](media/solution-template-move-files/move-files4.png)

5. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.   매개 변수는 파일을 이동하려는 폴더 경로와 파일을 이동할 폴더 경로입니다. 

    ![파이프라인 실행](media/solution-template-move-files/move-files5.png)

6. 결과를 검토합니다.

    ![결과 검토](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 팩터리에서 LastModifiedDate로 새 파일 및 변경된 파일 복사](solution-template-copy-new-files-lastmodifieddate.md)

- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)