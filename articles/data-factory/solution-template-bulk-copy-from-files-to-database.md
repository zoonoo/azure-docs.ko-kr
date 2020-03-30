---
title: 파일에서 데이터베이스로 대량 복사
description: 솔루션 템플릿을 사용하여 Azure Data Lake Storage Gen2에서 Azure 시냅스 분석/Azure SQL Database로 데이터를 대량으로 복사하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75921147"
---
# <a name="bulk-copy-from-files-to-database"></a>파일에서 데이터베이스로 대량 복사

이 문서에서는 Azure Data Lake Storage Gen2에서 Azure 시냅스 분석/Azure SQL Database로 데이터를 대량으로 복사하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 Azure Data Lake Storage Gen2 소스에서 파일을 검색합니다. 그런 다음 원본의 각 파일을 반복해서 대상 데이터 저장소에 파일을 복사합니다. 

현재 이 템플릿은 **DelimitedText** 형식의 데이터 복사만 지원합니다. 다른 데이터 형식의 파일은 원본 데이터 저장소에서 검색할 수도 있지만 대상 데이터 저장소에 복사할 수는 없습니다.  

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **메타데이터 받기** 활동은 Azure Data Lake Storage Gen2에서 파일을 검색하고 후속 *ForEach* 활동으로 전달합니다.
- **ForEach** 활동은 *메타데이터 받기* 활동에서 파일을 가져와서 각 파일을 *복사* 활동으로 이량매합니다.
- **복사** 활동은 *ForEach* 활동에 상주하여 원본 데이터 저장소에서 대상 데이터 저장소로 각 파일을 복사합니다.

템플릿은 다음 두 매개 변수를 정의합니다.
- *SourceContainer는* Azure Data Lake Storage Gen2에서 데이터가 복사되는 루트 컨테이너 경로입니다. 
- *SourceDirectory는* Azure Data Lake Storage Gen2에서 데이터가 복사되는 루트 컨테이너 아래의 디렉터리 경로입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일에서 데이터베이스 템플릿으로 대량 복사로 이동합니다.** 원본 Gen2 저장소에 대한 **새** 연결을 만듭니다. "GetMetadataDataset" 및 "SourceDataset"는 원본 파일 저장소의 동일한 연결에 대한 참조입니다.

    ![원본 데이터 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 데이터를 복사하는 싱크 데이터 저장소에 대한 **새** 연결을 만듭니다.

    ![싱크 데이터 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. **이 템플릿 사용을**선택합니다.

    ![이 템플릿 사용](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 다음 예제와 같이 생성된 파이프라인이 표시됩니다.

    ![파이프라인 검토](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 위에서 언급한 **2단계에서** 데이터 대상으로 **Azure Synapse Analytics(이전의 SQL DW)를** 선택한 경우 SQL 데이터 웨어하우스 Polybase에서 요구하는 대로 스테이징을 위해 Azure Blob 저장소에 대한 연결을 입력해야 합니다. 다음 스크린샷과 같이 템플릿은 Blob 저장소에 대한 *저장소 경로를* 자동으로 생성합니다. 파이프라인실행 후 컨테이너가 만들어졌는지 확인합니다.
        
    ![Polybase 설정](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    ![**디버그**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 파이프라인 실행이 성공적으로 완료되면 다음 예제와 유사한 결과가 표시됩니다.

    ![결과 검토](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)