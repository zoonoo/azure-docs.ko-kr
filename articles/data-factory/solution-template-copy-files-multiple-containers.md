---
title: Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사 | Microsoft Docs
description: 솔루션 템플릿을 사용하여 Azure Data Factory를 통해 여러 컨테이너에서 파일을 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966813"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사

이 문서에 설명된 솔루션 템플릿은 파일 저장소 간에 여러 파일, 컨테이너 또는 버킷의 파일을 복사하는 데 도움이 됩니다. 예를 들어 AWS S3에서 Azure Data Lake Store로 Data Lake를 마이그레이션하려는 경우가 있습니다. 또는 한 Azure Blob Storage 계정에서 다른 Azure Blob Storage 계정으로 모든 항목을 복제하려는 경우도 있습니다. 이 템플릿은 이러한 사용 사례에 맞게 디자인되었습니다.

단일 컨테이너 또는 버킷에서 파일을 복사하려는 경우 **데이터 복사 도구**를 사용하여 단일 복사 작업으로 파이프라인을 만드는 것이 더 효율적입니다. 이 간단한 사용 사례에는 이 템플릿으로 충분합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 원본 스토리지 저장소의 컨테이너를 열거한 다음, 원본 스토리지 저장소에서 대상 저장소로 각 컨테이너를 복사합니다. 

이 템플릿은 다음 세 가지 작업을 포함합니다.
-   **GetMetadata** 작업 - 원본 스토리지 저장소를 검사하고 컨테이너 목록을 가져옵니다.
-   **ForEach** 작업 - **GetMetadata** 작업에서 컨테이너 목록을 가져온 다음, 목록을 반복하여 각 컨테이너를 복사 작업에 전달합니다.
-   **복사** 작업 - 원본 스토리지 저장소에서 대상 저장소로 각 컨테이너를 복사합니다.

이 템플릿은 다음 두 개의 매개 변수를 정의합니다.
-   *SourceFilePath* 매개 변수는 데이터 원본 저장소의 경로로, 여기서 컨테이너 또는 버킷 목록을 가져올 수 있습니다. 대부분의 경우 이 경로는 여러 컨테이너 폴더를 포함하는 루트 디렉터리입니다. 이 매개 변수의 기본값은 `/`입니다.
-   *DestinationFilePath* 매개 변수는 대상 저장소에서 파일이 복사되는 경로입니다. 이 매개 변수의 기본값은 `/`입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일 저장소 간에 여러 개의 파일 컨테이너 복사** 템플릿으로 이동한 다음, 원본 스토리지 저장소에 대한 **새 연결**을 만듭니다. 원본 스토리지 저장소는 여러 개의 컨테이너 또는 버킷에서 파일을 복사하려는 위치입니다.

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 대상 스토리지 저장소에 대한 **새 연결**을 만듭니다.

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. **이 템플릿 사용**을 클릭합니다.

    ![이 템플릿 사용](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

    ![파이프라인 표시](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. **디버그**를 클릭하고 **매개 변수**를 입력한 다음, **마침**을 클릭합니다.

    ![파이프라인 실행](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 결과를 검토합니다.

    ![결과 검토](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
