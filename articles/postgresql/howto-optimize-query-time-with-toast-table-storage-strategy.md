---
title: 알림 테이블 스토리지 전략을 사용하여 Azure Database for PostgreSQL 서버에서 쿼리 시간 최적화
description: 이 문서에서는 Azure Database for PostgreSQL 서버에서 알림 테이블 스토리지 전략을 사용하여 쿼리 시간을 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: dee8aaaef4b1998a7234a88d07ad5efbc79d050b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628841"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>TOAST 테이블 스토리지 전략을 사용하여 쿼리 시간 최적화 
이 문서에서는 TOAST 테이블 스토리지 전략을 사용하여 쿼리 시간을 최적화하는 방법을 설명합니다.

## <a name="toast-table-storage-strategies"></a>TOAST 테이블 스토리지 전략
압축 및 아웃오브 라인 스토리지 간의 다양한 조합을 나타내는 디스크에 알림 가능한 열을 저장하는 네 가지 다른 전략이 있습니다. 전략은 데이터 형식 수준 및 열 수준에서 설정할 수 있습니다.
- **일반**은 압축 또는 아웃오브 라인 스토리지를 방지하며 또한 varlena 형식에 대한 싱글바이트 헤더를 사용하지 않도록 설정합니다. **일반**은 알림이 가능하지 않은 데이터 형식의 열에 대한 유일한 전략입니다.
- **확장**은 압축 및 아웃오브 라인 스토리지를 모두 허용합니다. **확장**은 대부분의 알림 가능한 데이터 형식의 기본값입니다. 먼저 압축을 시도한 다음, 행이 너무 큰 경우 아웃오브 라인 스토리지를 시도합니다.
- **외부**는 압축이 아닌 아웃오브 라인 스토리지만 허용합니다. **외부**를 사용하면 증가된 스토리지 공간에 대한 페널티로 와이드 텍스트 및 bytea 열에서 부분 문자열 작업이 가속화됩니다. 아웃오브 라인 값이 압축되지 않은 경우 이러한 작업이 해당 값의 필요한 부분만 페치하도록 최적화됐기 때문입니다.
- **기본**은 아웃오브 라인 스토리지가 아닌 압축만 허용합니다. 아웃오브 라인 스토리지는 여전히 이러한 열에 대해 수행되지만 행을 페이지에 적합하도록 작게 만들 방법이 없는 경우에 마지막 수단으로만 사용됩니다.

## <a name="using-toast-table-storage-strategies"></a>TOAST 테이블 스토리지 전략 사용
쿼리가 알림 가능한 데이터 형식에 액세스하는 경우 쿼리 시간을 줄이려면 기본 **확장** 옵션 대신 **기본**을 사용하는 것이 좋습니다. **기본**을 사용하면 아웃오브 라인 스토리지가 차단되지 않습니다. 반면, 쿼리가 알림 가능한 데이터 형식에 액세스하지 않는 경우 **확장** 옵션을 유지하는 것이 좋습니다. 따라서 기본 테이블에서 행의 큰 부분이 공유 버퍼 캐시에 적합하고 성능을 향상시킵니다.

넓은 테이블 및 많은 문자 개수가 포함된 스키마를 사용하는 워크로드가 있는 경우 PostgreSQL TOAST 테이블을 사용하는 것이 좋습니다. 고객 테이블의 예에는 255자가 포함된 여러 열이 있는 350개 이상의 열이 있습니다. 벤치 마크 쿼리 시간은 **기본**의 TOAST 전략을 변환한 후 4,203초에서 467초로 감소하여 89퍼센트 개선됐습니다.

## <a name="next-steps"></a>다음 단계
위의 특성은 워크로드를 검토합니다. 

PostgreSQL 설명서 [68장: 데이터베이스 실제 스토리지](https://www.postgresql.org/docs/current/storage-toast.html)를 검토합니다. 