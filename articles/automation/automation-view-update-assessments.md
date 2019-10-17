---
title: Azure 업데이트 관리 업데이트 평가 보기
description: 이 문서에서는 업데이트 배포에 대 한 업데이트 평가를 보는 방법을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377721"
---
# <a name="view-azure-update-management-update-assessments"></a>Azure 업데이트 관리 업데이트 평가 보기

Automation 계정에서 **업데이트 관리**를 선택하여 컴퓨터 상태를 확인합니다.

이 보기에서는 컴퓨터, 누락 업데이트, 업데이트 배포 및 예약된 업데이트 배포에 대한 정보를 제공합니다. **준수 열**에서 컴퓨터를 마지막으로 평가한 시간을 확인할 수 있습니다. **에이전트 업데이트 준비** 열에서 에이전트 업데이트의 상태를 확인할 수 있습니다. 문제가 있는 경우 링크를 선택하여 문제를 해결하기 위해 수행할 단계를 알려주는 문제 해결 설명서로 이동합니다.

컴퓨터, 업데이트 또는 배포에 대한 정보를 반환하는 로그 검색을 실행하려면 목록에서 항목을 선택합니다. 이렇게 하면 선택한 항목에 대한 쿼리가 있는 **로그 검색** 창이 열립니다.

![업데이트 관리 기본 보기](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 선택하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되며 선택 가능합니다. 업데이트가 필요한 컴퓨터 수, 운영 체제 및 세부 정보에 대한 링크와 관련된 정보가 표시됩니다. **로그 검색** 창에 업데이트에 대한 자세한 정보가 표시됩니다.

![누락 된 업데이트](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>업데이트 분류

다음 표에는 각 분류에 대한 정의와 함께 업데이트 관리의 업데이트 분류가 나열됩니다.

### <a name="windows"></a>Windows

|분류  |설명  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

### <a name="linux-2"></a>Linux

|분류  |설명  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 특성 또는 보안 업데이트에 중요하지 않은 기타 모든 업데이트입니다.        |

Linux의 경우 클라우드의 풍부한 데이터로 인해 평가 데이터가 표시되면서, 업데이트 관리가 클라우드에서 중요 및 보안 업데이트 간을 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS에서는 기본적으로 이 정보를 사용할 수 없습니다. CentOS 컴퓨터가 다음 명령에 대해 보안 데이터를 반환하는 방식으로 구성된 경우 업데이트 관리는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재, CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드 지원 메서드가 없습니다. 따라서 이러한 기능을 직접 사용하도록 설정했을 수 있는 고객에게만 최선의 지원이 제공됩니다.

## <a name="next-steps"></a>다음 단계

업데이트 평가를 확인 한 후에는 [Azure vm에 대 한 업데이트 및 패치 관리의](automation-tutorial-update-management.md)단계에 따라 업데이트 배포를 예약할 수 있습니다.