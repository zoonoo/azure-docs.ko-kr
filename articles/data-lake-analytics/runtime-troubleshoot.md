---
title: Azure 데이터 레이크 분석 U-SQL 런타임 오류 문제를 해결하는 방법
description: U-SQL 런타임 오류 문제를 해결하는 방법을 알아봅니다.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648455"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>런타임 변경으로 인한 U-SQL 런타임 오류 해결 방법 알아보기

컴파일러, 최적화 프로그램 및 작업 관리자를 포함한 Azure Data Lake U-SQL 런타임은 U-SQL 코드를 처리하는 것입니다.

## <a name="choosing-your-u-sql-runtime-version"></a>U-SQL 런타임 버전 선택

Visual Studio, ADL SDK 또는 Azure Data Lake Analytics 포털에서 U-SQL 작업을 제출하면 작업이 현재 사용 가능한 기본 런타임을 사용합니다. U-SQL 런타임의 새 버전은 정기적으로 릴리스되며 사소한 업데이트와 보안 수정 사항을 모두 포함합니다.

사용자 지정 런타임 버전을 선택할 수도 있습니다. 새 업데이트를 사용해 보거나 이전 버전의 런타임을 유지해야 하거나 일반 새 업데이트를 기다릴 수 없는 보고된 문제에 대한 핫픽스가 제공되었기 때문입니다.

> [!CAUTION]
> 기본값과 다른 런타임을 선택하면 U-SQL 작업이 중단될 수 있습니다. 이러한 다른 버전은 테스트에만 사용하십시오.

드문 경우지만 Microsoft 지원은 계정의 기본값으로 다른 버전의 런타임을 고정할 수 있습니다. 가능한 한 빨리 이 핀을 되돌리십시오. 해당 버전에 고정된 상태로 유지되면 나중에 만료됩니다.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>작업 U-SQL 런타임 버전 모니터링

Visual Studio의 작업 브라우저 또는 Azure 포털의 작업 기록을 통해 계정의 작업 기록에 과거 작업이 사용한 런타임 버전의 기록을 볼 수 있습니다.

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **모든 작업 보기를**선택합니다. 계정의 모든 활성 및 최근에 완료된 작업 목록이 나타납니다.
3. 필요에 따라 **필터**를 클릭하여 **시간 범위**, **작업 이름** 및 **작성자** 값을 기준으로 작업을 찾을 수 있습니다.
4. 완료된 작업에 사용된 런타임을 볼 수 있습니다.

![지난 작업의 런타임 버전 표시](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

사용 가능한 런타임 버전은 시간이 지남에 따라 변경됩니다. 기본 런타임은 항상 "기본값"이라고 하며, 여러 가지 이유로 특수 런타임을 사용할 수 있도록 하기 위해 적어도 이전 런타임을 사용할 수 있도록 합니다. 명시적으로 명명된 런타임은 일반적으로 다음 형식을 따릅니다(기울임꼴은 가변 부품에 사용되며 [] 선택적 부품을 나타냅니다).

release_YYYYMMDD_adl_buildno[_modifier]

예를 들어 release_20190318_adl_3394512_2 2019년 3월 18일 릴리스의 빌드 3394512의 두 번째 버전을 의미하고 release_20190318_adl_3394512_private 동일한 릴리스의 개인 빌드를 의미합니다. 참고: 날짜는 해당 릴리스에 대해 마지막 체크인이 수행된 날짜와 관련이 있으며 반드시 공식 릴리스 날짜는 아닙니다.

다음은 현재 사용 가능한 런타임 버전입니다.

- release_20190318_adl_3394512
- 현재 기본값 release_20190318_adl_5832669
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>U-SQL 런타임 버전 문제 해결

다음과 같은 두 가지 런타임 버전 문제가 발생할 수 있습니다.

1. 스크립트 또는 일부 사용자 코드는 동작을 한 릴리스에서 다음 릴리스로 변경합니다. 이러한 주요 변경 사항은 일반적으로 릴리스 노트의 게시와 함께 미리 전달됩니다. 이러한 주요 변경 이 발생하는 경우 Microsoft 지원에 문의하여 이 주요 동작을 보고하고(아직 문서화되지 않은 경우) 이전 런타임 버전에 대해 작업을 제출하십시오.

2. 계정에 고정된 경우 기본이 아닌 런타임을 명시적으로 또는 암시적으로 사용하고 있으며 해당 런타임은 얼마 후 제거되었습니다. 런타임이 누락된 경우 현재 기본 런타임으로 실행되도록 스크립트를 업그레이드하십시오. 추가 시간이 필요한 경우 Microsoft 지원에 문의하십시오.

## <a name="see-also"></a>참조

- [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-portal.md)
- [Azure Portal을 사용하여 Azure Data Lake Analytics에서 작업 모니터링](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
