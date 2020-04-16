---
title: 여러 컨테이너의 파일 복사
description: 솔루션 템플릿을 사용하여 Azure Data Factory를 사용하여 여러 컨테이너에서 파일을 복사하는 방법을 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414812"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 파일 저장소 간에 여러 컨테이너에서 파일을 복사하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명합니다. 예를 들어 이를 사용하여 AWS S3에서 Azure Data Lake Store로 데이터 레이크를 마이그레이션할 수 있습니다. 또는 템플릿을 사용하여 한 Azure Blob 저장소 계정에서 다른 저장소 계정으로 모든 것을 복제할 수 있습니다.

> [!NOTE]
> 단일 컨테이너에서 파일을 복사하려는 경우 [데이터 복사 도구를](copy-data-tool.md) 사용하여 단일 복사 활동이 있는 파이프라인을 만드는 것이 더 효율적입니다. 이 문서의 템플릿은 이 간단한 시나리오에 필요한 것 이상입니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 원본 저장소 저장소에서 컨테이너를 개예합니다. 그런 다음 해당 컨테이너를 대상 저장소에 복사합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **GetMetadata** 원본 저장소 저장소를 검사 하 고 컨테이너 목록을 가져옵니다.
- **ForEach는** **GetMetadata** 활동에서 컨테이너 목록을 가져온 다음 목록을 반복해서 각 컨테이너를 복사 활동에 전달합니다.
- 원본 저장소 저장소에서 대상 저장소로 각 컨테이너를 **복사합니다.**

템플릿은 다음 매개 변수를 정의합니다.
- *SourceFileFolder는* 컨테이너 목록을 얻을 수 있는 데이터 원본 저장소의 폴더 경로입니다. 경로는 여러 컨테이너 폴더를 포함하는 루트 디렉터리입니다. 이 매개 변수의 기본값은 `sourcefolder`입니다.
- *SourceFileDirectory는* 데이터 원본 저장소의 루트 디렉터리 아래의 하위 폴더 경로입니다. 이 매개 변수의 기본값은 `subfolder`입니다.
- *DestinationFileFolder는* 파일이 대상 저장소에 복사될 폴더 경로입니다. 이 매개 변수의 기본값은 `destinationfolder`입니다.
- *DestinationFileDirectory는* 파일이 대상 저장소에 복사되는 하위 폴더 경로입니다. 이 매개 변수의 기본값은 `subfolder`입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 파일 저장소 템플릿 **간에 여러 파일 컨테이너 복사로** 이동합니다. 원본 저장소 저장소에 대한 **새** 연결을 만듭니다. 원본 저장소 저장소는 여러 컨테이너에서 파일을 복사하려는 위치입니다.

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 대상 저장소 저장소에 대한 **새** 연결을 만듭니다.

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. **이 템플릿 사용을**선택합니다.

    ![이 템플릿 사용](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 다음 예제와 같이 파이프라인이 표시됩니다.

    ![파이프라인 표시](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    ![파이프라인 실행](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 결과를 검토합니다.

    ![결과 검토](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 팩터리의 컨트롤 테이블을 사용하여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
