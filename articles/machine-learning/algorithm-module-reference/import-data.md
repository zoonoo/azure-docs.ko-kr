---
title: '데이터 가져오기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스의 데이터 가져오기 모듈을 사용 하 여 기존 클라우드 데이터 서비스에서 Machine Learning 파이프라인으로 데이터를 로드 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693144"
---
# <a name="import-data-module"></a>데이터 가져오기 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 기존 클라우드 데이터 서비스에서 machine learning 파이프라인으로 데이터를 로드할 수 있습니다.  

처음에는 읽고 있는 클라우드 기반 저장소의 유형을 선택 하 고 추가 설정을 완료 합니다. 원하는 데이터를 정의 하 고 원본에 연결 하면 [데이터 가져오기](./import-data.md) 는 포함 된 값을 기준으로 각 열의 데이터 형식을 유추 하 고 Azure Machine Learning 작업 영역에 데이터를 로드 합니다. [데이터 가져오기](./import-data.md) 의 출력은 모든 파이프라인에서 사용할 수 있는 데이터 집합입니다.

  
원본 데이터가 변경 되 면 데이터 집합을 새로 고치고 [데이터 가져오기](./import-data.md)를 다시 실행 하 여 새 데이터를 추가할 수 있습니다. 그러나 파이프라인을 실행할 때마다 원본에서 다시 읽지 않으려는 경우 **캐시 된 결과 사용** 옵션을 TRUE로 선택 합니다. 이 옵션을 선택 하면 모듈은 파이프라인이 동일한 소스와 동일한 입력 옵션을 사용 하 여 이전에 실행 되었는지 여부를 확인 합니다. 이전 실행을 찾은 경우 원본에서 데이터를 다시 로드 하는 대신 캐시의 데이터가 사용 됩니다.
 

## <a name="data-sources"></a>데이터 원본

데이터 가져오기 모듈은 다음과 같은 데이터 원본을 지원 합니다. 각 데이터 원본을 사용 하는 방법에 대 한 자세한 지침과 예제를 보려면 링크를 클릭 하십시오. 
 
데이터를 어디에 저장 해야 하는지 확실 하지 않은 경우 데이터 과학 프로세스의 일반적인 데이터 시나리오에 대 한이 가이드 인 [Azure Machine Learning의 고급 분석 시나리오](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios)를 참조 하세요. 


|데이터 원본| 사용 방법|
|-----------|-----------|  
|[HTTP를 통한 웹 URL](./import-from-web-url-via-http.md)|HTTP를 사용 하 고 CSV, TSV, ARFF 또는 SvmLight 형식으로 제공 된 웹 URL에서 호스트 되는 데이터를 가져옵니다.|  
|[Azure Blob Storage에서 가져오기](./import-from-azure-blob-storage.md) |Azure blob service에 저장 된 데이터 가져오기|  
|[Azure SQL Database에서 가져오기](./import-from-azure-sql-database.md) |Azure SQL Database에서 데이터 가져오기|

## <a name="how-to-configure-import-data"></a>데이터 가져오기를 구성 하는 방법
 
1. 파이프라인에 **데이터 가져오기** 모듈을 추가 합니다. 이 모듈은 인터페이스의 **데이터 입력 및 출력** 범주에서 찾을 수 있습니다.

1. **데이터 원본**을 클릭 하 고 읽을 클라우드 기반 저장소의 유형을 선택 합니다. 

    추가 설정은 선택한 저장소 유형 및 저장소의 보안 여부에 따라 달라 집니다. 계정 이름, 파일 형식 또는 자격 증명을 제공 해야 할 수도 있습니다. 일부 소스에는 인증이 필요 하지 않습니다. 다른 사용자의 경우 계정 이름, 키 또는 컨테이너 이름을 알아야 할 수 있습니다.

1. 연속 실행에서 다시 사용할 데이터 집합을 캐시 하려면 **캐시 된 결과 사용** 옵션을 선택 합니다.

    모듈 매개 변수에 대 한 다른 변경 내용이 없는 경우 파이프라인은 모듈이 처음 실행 될 때만 데이터를 로드 하 고 이후에는 캐시 된 버전의 데이터 집합을 사용 합니다.

    파이프라인을 실행할 때마다 데이터를 다시 로드 해야 하는 경우이 옵션의 선택을 취소 합니다.

1. 파이프라인을 실행합니다.

    데이터 가져오기는 인터페이스에 데이터를 로드 하는 경우 포함 된 값을 기준으로 각 열의 데이터 형식 (숫자 또는 범주)을 유추 합니다.

    - 헤더가 있는 경우 헤더는 출력 데이터 집합의 열 이름을 표시 하는 데 사용 됩니다.

    - 데이터에 기존 열 머리글이 없으면 col1, col2 형식을 사용 하 여 새 열 이름이 생성 됩니다,... , coln *.

## <a name="results"></a>결과

가져오기가 완료 되 면 출력 데이터 집합을 클릭 하 고 **시각화** 를 선택 하 여 데이터를 성공적으로 가져왔는지 확인 합니다.

다시 사용 하기 위해 데이터를 저장 하려는 경우 파이프라인이 실행 될 때마다 새 데이터 집합을 가져오는 대신, 출력을 마우스 오른쪽 단추로 클릭 하 고 **데이터 집합으로 저장**을 선택 합니다. 데이터 집합의 이름을 선택 합니다. 저장 된 데이터 집합은 저장 시 데이터를 유지 하 고 파이프라인의 데이터 집합이 변경 되더라도 파이프라인이 다시 실행 될 때 데이터가 업데이트 되지 않습니다. 이는 데이터의 스냅숏을 만드는 데 유용할 수 있습니다.

데이터를 가져온 후 모델링 및 분석을 위해 다음과 같은 몇 가지 추가 준비가 필요할 수 있습니다.


- [메타 데이터 편집](./edit-metadata.md) 을 사용 하 여 열 이름을 변경 하거나, 열을 다른 데이터 형식으로 처리 하거나, 일부 열이 레이블이나 기능 임을 나타낼 수 있습니다.

- [데이터 집합의 열 선택](./select-columns-in-dataset.md) 을 사용 하 여 모델링에 사용할 열 하위 집합을 선택 합니다. [열 추가](./add-columns.md) 모듈을 사용 하 여 변환 되거나 제거 된 열을 원래 데이터 집합에 쉽게 다시 연결할 수 있습니다.  

- [파티션 및 샘플](./partition-and-sample.md) 을 사용 하 여 데이터 집합을 나누거나 샘플링을 수행 하거나 상위 n 개 행을 가져옵니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 