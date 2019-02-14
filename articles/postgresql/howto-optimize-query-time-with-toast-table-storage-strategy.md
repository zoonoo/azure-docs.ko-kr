---
title: TOAST 테이블 스토리지 전략을 사용하여 Azure Database for PostgreSQL 서버에서 쿼리 시간 최적화
description: 이 문서에서는 Azure Database for PostgreSQL 서버에서 TOAST 테이블 스토리지 전략을 사용하여 쿼리 시간을 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 96793cb1785a7ffa86331285f401453641b50dac
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820877"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>TOAST 테이블 스토리지 전략으로 쿼리 시간 최적화 
이 문서에서는 TOAST(너무 큰 특성 스토리지 기술) 테이블 스토리지 전략을 사용하여 쿼리 시간을 최적화하는 방법을 설명합니다.

## <a name="toast-table-storage-strategies"></a>TOAST 테이블 스토리지 전략
TOAST를 사용할 수 있는 열을 디스크에 저장하는 데 네 가지 전략이 사용됩니다. 압축 및 아웃오브 라인 스토리지 간의 다양한 조합을 나타냅니다. 전략은 데이터 형식 수준 및 열 수준에서 설정할 수 있습니다.
- **일반**은 압축 또는 아웃오브 라인 스토리지를 방지합니다. varlena 유형에 대한 싱글 바이트 헤더를 사용할 수 없습니다. 일반은 TOAST를 사용할 수 없는 데이터 형식의 열에 대해서만 가능한 전략입니다.
- **확장**은 압축 및 아웃오브 라인 스토리지를 모두 허용합니다. 확장은 TOAST를 사용할 수 있는 대부분의 데이터 형식에 대한 기본값입니다. 압축이 먼저 시도됩니다. 행이 여전히 너무 크면 아웃오브 라인 스토리지가 시도됩니다.
- **외부**는 압축이 아닌 아웃오브 라인 스토리지만 허용합니다. 외부를 사용하면 넓은 텍스트 및 바이트 열에 대해 하위 문자열 작업을 빠르게 수행합니다. 속도가 빨라지면 스토리지 공간이 증가되는 단점이 있습니다. 이러한 작업은 압축되지 않은 경우 아웃오브 라인 값의 필수 부분만 가져오도록 최적화되어 있습니다.
- **기본**은 아웃오브 라인 스토리지가 아닌 압축만 허용합니다. 아웃오브 라인 스토리지는 그러한 열에 대해 여전히 수행되지만 최후의 수단으로만 수행됩니다. 행을 페이지에 들어갈 만큼 작게 만들 수 있는 다른 방법이 없을 때 발생합니다.

## <a name="use-toast-table-storage-strategies"></a>TOAST 테이블 스토리지 전략 사용
쿼리가 TOAST를 사용할 수 있는 데이터 형식에 액세스하는 경우 기본 확장 옵션 대신 기본 전략을 사용하여 쿼리 시간을 줄이는 것이 좋습니다. 기본은 아웃오브 라인 스토리지를 배제하지 않습니다. 쿼리가 TOAST를 사용할 수 있는 데이터 형식에 액세스하지 않으면 확장 옵션을 유지하는 것이 좋습니다. 따라서 기본 테이블의 행 중 큰 부분이 공유 버퍼 캐시에 적합하고 성능을 향상시킵니다.

넓은 테이블 및 문자 수가 많이 포함된 스키마를 사용하는 워크로드가 있는 경우 PostgreSQL TOAST 테이블을 사용하는 것이 좋습니다. 고객 테이블의 예에는 255자가 넘는 여러 열이 있는 350개를 초과하는 열이 있습니다. TOAST 테이블 기본 전략으로 변환된 후, 벤치마크 쿼리 시간이 4203초에서 467초로 단축되었습니다. 이것은 89%가 개선된 것입니다.

## <a name="next-steps"></a>다음 단계
이전 특성에 대한 워크로드를 검토합니다. 

다음 PostgreSQL 설명서를 검토합니다. 
- [Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html)(68장, 데이터베이스 물리적 스토리지) 