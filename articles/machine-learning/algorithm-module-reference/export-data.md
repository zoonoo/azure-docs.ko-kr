---
title: '데이터 내보내기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 데이터 내보내기 모듈을 사용하여 Azure Machine Learning 외부에 결과 및 중간 데이터를 저장할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 82821b29669139f378d4dd24e4a96ab66f3d56e1
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108321936"
---
# <a name="export-data-module"></a>데이터 내보내기 모듈

이 문서에서는 Azure Machine Learning 디자이너의 모듈에 대해 설명합니다.

이 모듈을 사용하여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 클라우드 스토리지 대상에 저장합니다. 

이 모듈은 데이터를 다음 클라우드 데이터 서비스로 내보내는 작업을 지원합니다.

- Azure Blob 컨테이너
- Azure 파일 공유
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL 데이터베이스

데이터를 내보내기 전에 Azure Machine Learning 작업 영역에 데이터 저장소를 먼저 등록해야 합니다. 자세한 내용은 [Azure Storage 서비스의 데이터 액세스](../how-to-access-data.md)를 참조하세요.

## <a name="how-to-configure-export-data"></a>내보내기 데이터를 구성하는 방법

1. 디자이너에서 **데이터 내보내기** 모듈을 파이프라인에 추가합니다. **입력 및 출력** 범주에서 이 모듈을 찾을 수 있습니다.

1. 내보낼 데이터를 포함하는 모듈에 **데이터 내보내기** 를 연결합니다.

1. **데이터 내보내기** 를 선택하여 **속성** 창을 엽니다.

1. **데이터 저장소** 의 경우 드롭다운 목록에서 기존 데이터 저장소를 선택합니다. 새 데이터 저장소를 만들 수도 있습니다. [Azure Storage 서비스의 데이터 액세스](../how-to-access-data.md)를 방문하여 방법을 확인합니다.

    > [!NOTE]
    > 특정 데이터 형식의 데이터를 다른 데이터 형식으로 지정된 SQL 데이터베이스 열로 내보내는 것은 지원되지 않습니다. 대상 테이블이 먼저 존재해야 할 필요는 없습니다.

1. **출력 다시 생성** 확인란은 실행 시간에 출력을 다시 생성하기 위해 모듈을 실행할지 여부를 결정합니다. 

    기본적으로 이 옵션은 선택 취소되어 있습니다. 즉, 모듈이 이전에 동일한 매개 변수를 사용하여 실행된 경우 시스템은 마지막 실행의 출력을 다시 사용하여 실행 시간을 줄입니다. 

    이 확인란이 선택되면 시스템에서 모듈을 다시 실행하여 출력을 다시 생성합니다.

1. 데이터 저장소에서 데이터가 있는 경로를 정의합니다. 이 경로는 상대 경로입니다. `data/testoutput`을 예로 들어 보겠습니다. 여기서 **데이터 내보내기** 의 입력 데이터가 모듈의 **출력 설정** 에서 설정하는 데이터 저장소의 `data/testoutput`으로 내보내집니다.

    > [!NOTE]
    > 빈 경로 또는 **URL 경로** 는 허용되지 않습니다.


1. **파일 형식** 에 대해 데이터를 저장할 형식을 선택합니다.
 
1. 파이프라인을 제출합니다.

## <a name="limitations"></a>제한 사항

데이터 저장소 액세스 제한으로 인해 유추 파이프라인에 **데이터 내보내기** 모듈이 포함되어 있으면 실시간 엔드포인트에 배포할 때 자동으로 제거됩니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
