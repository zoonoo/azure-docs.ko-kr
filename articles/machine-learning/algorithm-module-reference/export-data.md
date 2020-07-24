---
title: '데이터 내보내기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 데이터 내보내기 모듈을 사용 하 여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 Azure Machine Learning 외부의 클라우드 저장소 대상에 저장 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 279ba2ca5b82d7bad5b55736179f92a0146bdc84
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048155"
---
# <a name="export-data-module"></a>데이터 내보내기 모듈

이 문서에서는 Azure Machine Learning 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용 하 여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 클라우드 저장소 대상에 저장 합니다. 

이 모듈은 데이터를 다음 클라우드 데이터 서비스로 내보내는 작업을 지원 합니다.

- Azure Blob 컨테이너
- Azure 파일 공유
- Azure Data Lake
- Azure Data Lake Gen2
- Azure SQL 데이터베이스

데이터를 내보내기 전에 Azure Machine Learning 작업 영역에 데이터 저장소를 먼저 등록 해야 합니다. 자세한 내용은 [Azure storage 서비스에서 데이터 액세스](../how-to-access-data.md)를 참조 하세요.

## <a name="how-to-configure-export-data"></a>내보내기 데이터를 구성 하는 방법

1. 디자이너에서 **데이터 내보내기** 모듈을 파이프라인에 추가 합니다. 이 모듈은 **입력 및 출력** 범주에서 찾을 수 있습니다.

1. 내보낼 데이터를 포함 하는 모듈에 **내보내기 데이터** 를 연결 합니다.

1. **데이터 내보내기** 를 선택 하 여 **속성** 창을 엽니다.

1. **데이터 저장소**의 경우 드롭다운 목록에서 기존 데이터 저장소를 선택 합니다. 새 데이터 저장소를 만들 수도 있습니다. [Azure storage 서비스에서 데이터 액세스](../how-to-access-data.md)를 방문 하 여 방법을 확인 합니다.

1. **출력 다시 생성**확인란은 실행 시 출력을 다시 생성 하기 위해 모듈을 실행할지 여부를 결정 합니다. 

    기본적으로 선택 취소 되어 있습니다. 즉, 모듈이 이전에 동일한 매개 변수를 사용 하 여 실행 되는 경우 시스템은 마지막 실행의 출력을 다시 사용 하 여 실행 시간을 줄입니다. 

    이 확인란이 선택 되어 있으면 시스템에서 모듈을 다시 실행 하 여 출력을 다시 생성 합니다.

1. 데이터 저장소에서 데이터가 인 경로를 정의 합니다. 경로가 상대 경로입니다. 빈 경로 또는 URL 경로는 허용 되지 않습니다.


1. **파일 형식**에 대해 데이터를 저장할 형식을 선택 합니다.
 
1. 파이프라인을 제출합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
