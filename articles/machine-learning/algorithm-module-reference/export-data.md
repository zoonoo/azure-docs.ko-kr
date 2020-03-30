---
title: '데이터 내보내기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 데이터 내보내기 모듈을 사용하여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 Azure Machine Learning 외부의 클라우드 저장소 대상으로 저장하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456304"
---
# <a name="export-data-module"></a>데이터 내보내기 모듈

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 클라우드 저장소 대상으로 저장합니다. 

이 모듈은 다음 클라우드 데이터 서비스로 데이터 내보내기를 지원합니다.

- Azure Blob 컨테이너
- Azure 파일 공유
- Azure 데이터 레이크
- Azure Data Lake Gen2

데이터를 내보내기 전에 먼저 Azure 기계 학습 작업 영역에 데이터 스토어를 등록해야 합니다. 자세한 내용은 [Azure 저장소 서비스의 액세스 데이터를](../how-to-access-data.md)참조하십시오.

## <a name="how-to-configure-export-data"></a>내보내기 데이터 구성 방법

1. 디자이너의 파이프라인에 **데이터 내보내기** 모듈을 추가합니다. 이 모듈은 입력 **및 출력** 범주에서 찾을 수 있습니다.

1. **내보낼 데이터가** 포함된 모듈에 데이터 내보내기를 연결합니다.

1. **속성** 창을 열려면 **데이터 내보내기를** 선택합니다.

1. **Datastore의**경우 드롭다운 목록에서 기존 데이터스토어를 선택합니다. 새 데이터스토어를 만들 수도 있습니다. Azure 저장소 [서비스의 Access 데이터를](../how-to-access-data.md)방문하여 방법을 확인합니다.

1. **출력**재생확인란, 실행 시간에 출력을 재생성할 모듈을 실행할지 여부를 결정합니다. 

    기본적으로 선택되지 않은 모듈이 이전에 동일한 매개 변수로 실행된 경우 시스템은 마지막 실행의 출력을 재사용하여 런타임을 줄입니다. 

    이 옵션을 선택하면 시스템이 모듈을 다시 실행하여 출력을 재생성합니다.

1. 데이터가 있는 데이터 스토어에서 경로를 정의합니다. 경로는 상대 경로입니다. 빈 경로 또는 URL 경로는 허용되지 않습니다.


1. **파일 형식의**경우 데이터를 저장할 형식을 선택합니다.
 
1. 파이프라인을 제출합니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 
