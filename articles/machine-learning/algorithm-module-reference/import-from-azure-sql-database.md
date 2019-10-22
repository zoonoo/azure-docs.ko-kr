---
title: Azure SQL Database에서 가져오기
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 시각적 인터페이스의 데이터 가져오기 모듈을 사용 하 여 Azure SQL Database에서 데이터를 가져오는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694611"
---
# <a name="import-from-azure-sql-database"></a>Azure SQL Database에서 가져오기

이 문서에서는 Azure Machine Learning 시각적 인터페이스의 [데이터 가져오기](import-data.md) 모듈을 사용 하 여 Azure SQL Database에서 데이터를 가져오는 방법을 설명 합니다.  

데이터베이스에서 데이터를 가져오려면 서버 이름과 데이터베이스 이름을 모두 지정 하 고 테이블, 뷰 또는 쿼리를 정의 하는 SQL 문을 지정 해야 합니다.  

일반적으로 azure 데이터베이스에 데이터를 저장 하는 것은 Azure에서 테이블이 나 blob을 사용 하는 것 보다 비용이 많이 듭니다. 또한 구독 유형에 따라 데이터베이스에 저장할 수 있는 데이터의 양에 제한이 있을 수 있습니다. 그러나 SQL Azure 데이터베이스에 대 한 트랜잭션 요금은 없으므로이 옵션은 데이터 조회 테이블 또는 데이터 사전과 같은 더 적은 양의 자주 사용 되는 정보에 빠르게 액세스 하는 데 적합 합니다.

데이터를 읽기 전에 필터링 해야 하거나, 보고를 위해 예측 또는 메트릭을 데이터베이스에 다시 저장 하려는 경우에는 Azure 데이터베이스에 데이터를 저장 하는 것도 좋습니다.

## <a name="how-to-import-data-from-azure-sql-database"></a>Azure SQL Database에서 데이터를 가져오는 방법

1. 파이프라인에 [데이터 가져오기](import-data.md) 모듈을 추가 합니다. 이 모듈은 데이터 입력 및 출력 범주의 시각적 인터페이스에서 찾을 수 있습니다.

1. **데이터 원본**에 대해 **Azure SQL Database**를 선택 합니다.

1. Azure SQL Database와 관련 된 다음 옵션을 설정 합니다.

    **데이터베이스 서버 이름**: Azure에서 생성 된 서버 이름을 입력 합니다. 일반적으로 `<generated_identifier>.database.windows.net` 형식이 있습니다.

    **데이터베이스 이름**: 지정한 서버에 있는 기존 데이터베이스의 이름을 입력 합니다.

    **서버 사용자 계정 이름**: 데이터베이스에 대 한 액세스 권한이 있는 계정의 사용자 이름을 입력 합니다.

    **서버 사용자 계정 암호**: 지정 된 사용자 계정에 대 한 암호를 제공 합니다.

    **데이터베이스 쿼리**: 읽을 데이터를 설명 하는 SQL 문을 입력 하거나 붙여 넣습니다. Visual Studio 서버 탐색기 또는 SQL Server Data Tools와 같은 도구를 사용 하 여 항상 SQL 문의 유효성을 검사 하 고 쿼리 결과를 확인 합니다.

1. Azure Machine Learning으로 읽어 온 데이터 집합이 파이프라인 실행 간에 변경 될 수 없는 경우 **캐시 된 결과 사용** 옵션을 선택 합니다.

    모듈 매개 변수에 대 한 다른 변경 내용이 없는 경우 파이프라인은 모듈이 처음 실행 될 때 데이터를 로드 한 후 캐시 된 버전의 데이터 집합을 사용 합니다.

    파이프라인이 반복 될 때마다 데이터 집합을 다시 로드 하려면이 옵션을 선택 취소 합니다. [데이터 가져오기](import-data.md)에서 매개 변수가 변경 될 때마다 데이터 집합이 원본에서 다시 로드 됩니다.

1. 파이프라인을 실행합니다.

    [데이터를 가져올](import-data.md) 때 데이터를 시각적 인터페이스로 로드 하면 원본 데이터베이스에 사용 되는 데이터 형식에 따라 일부 암시적 형식 변환이 수행 될 수도 있습니다.

## <a name="results"></a>결과

가져오기가 완료 되 면 출력 데이터 집합을 클릭 하 고 **시각화** 를 선택 하 여 데이터를 성공적으로 가져왔는지 확인 합니다.

필요에 따라 시각적 인터페이스의 도구를 사용 하 여 데이터 집합 및 메타 데이터를 변경할 수 있습니다.

- [메타 데이터 편집](edit-metadata.md) 을 사용 하 여 열 이름을 변경 하거나, 열을 다른 데이터 형식으로 변환 하거나, 레이블이나 기능 열을 표시 합니다.

- [데이터 집합의 열 선택](select-columns-in-dataset.md) 을 사용 하 여 열의 하위 집합을 선택 합니다.

- [파티션 및 샘플](partition-and-sample.md) 을 사용 하 여 데이터 집합을 기준으로 구분 하거나 상위 n 개 행을 가져옵니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 
