---
title: '데이터 내보내기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 데이터 내보내기 모듈을 사용 하 여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 Azure Machine Learning 외부의 클라우드 저장소 대상에 저장 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5badcbe2a76c41bdc1431abae617e35b7555eb88
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497906"
---
# <a name="export-data-module"></a>데이터 내보내기 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 파이프라인의 결과, 중간 데이터 및 작업 데이터를 Azure Machine Learning 외부의 클라우드 저장소 대상에 저장 합니다. 

이 모듈은 데이터를 다음 클라우드 데이터 서비스로 내보내는 작업을 지원 합니다.

- Azure Blob 컨테이너
- Azure 파일 공유
- Azure 데이터 레이크
- Azure Data Lake Gen2

데이터를 내보내기 전에 먼저 Azure Machine Learning 작업 영역에 데이터 저장소를 등록 해야 합니다. 자세한 내용은 [데이터에 액세스 하는 방법](../service/how-to-access-data.md)을 참조 하세요.

## <a name="how-to-configure-export-data"></a>내보내기 데이터를 구성 하는 방법

1. 디자이너에서 **데이터 내보내기** 모듈을 파이프라인에 추가 합니다. 이 모듈은 **입력 및 출력** 범주에서 찾을 수 있습니다.

1. 내보낼 데이터를 포함 하는 모듈에 **내보내기 데이터** 를 연결 합니다.

1. **데이터 내보내기** 를 선택 하 여 **속성** 창을 엽니다.

1. **데이터 저장소**의 경우 드롭다운 목록에서 기존 데이터 저장소를 선택 합니다. 새 데이터 저장소를 만들 수도 있습니다. [액세스 방법-데이터를](../service/how-to-access-data.md) 방문 하 여 방법 확인

1. 데이터를 쓸 데이터 저장소의 경로를 정의 합니다. 


1. **파일 형식**에 대해 데이터를 저장할 형식을 선택 합니다.
 
1. 파이프라인을 실행합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 