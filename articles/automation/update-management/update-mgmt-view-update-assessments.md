---
title: Azure Automation 업데이트 평가 보기
description: 이 문서에서는 업데이트 관리를 배포하기 위한 업데이트 평가를 살펴보는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 92861304a946e357b2b265cd825eceb8e22f7d2d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450405"
---
# <a name="view-update-assessments"></a>업데이트 평가 보기

업데이트 관리에서 컴퓨터, 누락 된 업데이트, 업데이트 배포 및 예약 된 업데이트 배포에 대 한 정보를 볼 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com)

## <a name="view-update-assessment"></a>업데이트 평가 보기

업데이트 관리에서 컴퓨터, 누락 된 업데이트, 업데이트 배포 및 예약 된 업데이트 배포에 대 한 정보를 볼 수 있습니다.

[![업데이트 관리 기본 보기](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

업데이트 평가를 보려면 다음을 수행 합니다.

1. Azure Portal에서 **automation 계정** 으로 이동 하 여 목록에서 업데이트 관리 사용 하도록 설정 된 automation 계정을 선택 합니다.

2. Automation 계정의 왼쪽 창에서 **업데이트 관리** 를 선택 합니다.

3. 사용자 환경에 대 한 업데이트가 **업데이트 관리** 페이지에 나열 됩니다. 누락 된 것으로 식별 되는 업데이트의 목록이 **누락 된 업데이트** 탭에 표시 됩니다.

   **준수** 열에서 컴퓨터가 마지막으로 평가 된 시간을 확인할 수 있습니다. **에이전트 준비 상태 업데이트** 열에서 업데이트 에이전트의 상태를 확인할 수 있습니다. 문제가 있는 경우 링크를 선택하여 문제 해결을 도와주는 문제 해결 설명서로 이동합니다.

4. **정보 링크**에서 업데이트 링크를 선택하여 업데이트에 대한 중요한 정보를 제공하는 지원 문서를 엽니다.

     [![업데이트 상태 보기](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

5. 업데이트의 다른 위치를 클릭하면 로그 검색 창이 열립니다. 로그 검색에 대한 쿼리는 해당 특정 업데이트에 대해 미리 정의되어 있습니다. 이 쿼리를 수정하거나 자체 쿼리를 만들어 자세한 정보를 볼 수 있습니다.

    [![로그 쿼리 결과 보기](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 선택하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되며 선택 가능합니다. 업데이트가 필요한 머신 수, 운영 체제 세부 정보, 자세한 정보 링크가 표시됩니다. 로그 검색 창에는 업데이트에 대한 자세한 정보도 표시됩니다.

![누락 업데이트](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>업데이트 분류 작업

다음 표에는 각 분류에 대한 정의와 함께 업데이트 관리에서 지원하는 업데이트 분류가 나열되어 있습니다.

### <a name="windows"></a>Windows

|분류  |Description  |
|---------|---------|
|중요 업데이트     | 보안과 관련이 없는 중요한 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 보안과 관련이 있는 제품 관련 업데이트입니다.        |
|업데이트 롤업     | 간편하게 배포할 수 있도록 함께 패키지로 제공되는 핫픽스 세트입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스 세트입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일의 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치된 애플리케이션 또는 파일의 업데이트입니다.        |

### <a name="linux"></a>Linux

|분류  |Description  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 본질적으로 중요하지 않거나 보안 업데이트가 아닌 그 외의 모든 업데이트입니다.        |

Linux의 경우 평가 데이터를 표시하는 동안 업데이트 관리에서는 중요한 업데이트와 보안 업데이트를 구분할 수 있습니다. (이 세분성은 클라우드의 데이터 보강 때문에 가능합니다.) 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS는 제품의 RTM 버전에서 이 정보를 사용할 수 없습니다. CentOS 머신이 다음 명령에 대해 보안 데이터를 반환하도록 구성된 경우 업데이트 관리에서는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재는 CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드가 없습니다. 지금은 직접 이 기능을 사용하도록 설정한 고객에게만 최선의 지원이 제공됩니다.

Red Hat Enterprise 버전 6의 업데이트를 분류하려면 yum-보안 플러그 인을 설치해야 합니다. Red Hat Enterprise Linux 7에서는 플러그 인이 이미 yum 자체에 포함되어 있으므로 아무 것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술 항목](https://access.redhat.com/solutions/10021)을 참조하세요.

## <a name="next-steps"></a>다음 단계

프로세스의 다음 단계는 비규격 컴퓨터에 [업데이트를 배포](update-mgmt-deploy-updates.md) 하 고 배포 결과를 검토 하는 것입니다.
