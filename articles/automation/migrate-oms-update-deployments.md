---
title: Azure Portal로 Azure Monitor 로그 업데이트 배포 마이그레이션
description: 이 문서에서는 Azure Monitor 로그 업데이트 배포를 Azure Portal로 마이그레이션하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: dabe914ffd4e84e8ded5c0e3b519036479fe33ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987594"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Azure Portal로 Azure Monitor 로그 업데이트 배포 마이그레이션

OMS(Operations Management Suite) 포털은 [더 이상 사용되지 않습니다](../azure-monitor/platform/oms-portal-transition.md). 업데이트 관리를 위해 OMS 포털에서 제공되었던 모든 기능은 Azure Monitor 로그를 통해 Azure Portal에서 사용할 수 있습니다. 이 문서에서는 Azure Portal로 마이그레이션하는 데 필요한 정보를 제공합니다.

## <a name="key-information"></a>주요 정보

* 기존 배포는 계속 작동합니다. Azure에서 배포를 다시 만들고 나면 이전 배포를 삭제할 수 있습니다.
* OMS에 있던 모든 기존 기능을 Azure에서 사용할 수 있습니다. 업데이트 관리에 대한 자세한 내용은 [업데이트 관리 개요](update-management/update-mgmt-overview.md)를 참조하세요.

## <a name="access-the-azure-portal"></a>Azure Portal 액세스

1. 작업 영역에서 **Azure에서 열기**를 클릭합니다. 

    ![Azure에서 열기 - Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. Azure Portal에서 **Automation 계정**을 클릭합니다.

    ![Azure Monitor 로그](media/migrate-oms-update-deployments/log-analytics.png)

3. Automation 계정에서 **업데이트 관리**를 클릭합니다.

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="업데이트 관리 페이지의 스크린샷":::

4. Azure Portal에서 **모든 서비스** 아래의 **Automation 계정**을 선택합니다. 

5. **관리 도구**에서 적절한 Automation 계정을 선택한 다음 **업데이트 관리**를 클릭합니다.

## <a name="recreate-existing-deployments"></a>기존 배포 다시 만들기

OMS 포털에서 만든 모든 업데이트 배포에는 존재하는 업데이트 배포와 동일한 이름을 가진 [저장된 검색](../azure-monitor/platform/computer-groups.md)이 있으며, 이를 컴퓨터 그룹이라고도 합니다. 저장된 검색에는 업데이트 배포에서 예약된 머신 목록이 포함되어 있습니다.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="업데이트 관리 페이지의 스크린샷":::

이 기존의 저장된 검색을 사용하려면 다음 단계를 따르세요.

1. 새 업데이트 배포를 만들려면 Azure Portal로 이동하여 사용할 Automation 계정을 선택하고 **업데이트 관리**를 클릭합니다. **업데이트 배포 예약**을 클릭합니다.

    ![업데이트 배포 예약](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. 새 업데이트 배포 창이 열립니다. 다음 표에 설명된 속성의 값을 입력하고 **만들기**를 클릭합니다.

3. **업데이트할 컴퓨터**로 OMS 배포에서 사용하는 저장된 검색을 선택합니다.

    | 속성 | Description |
    | --- | --- |
    |속성 |업데이트 배포를 식별하는 고유 이름입니다. |
    |운영 체제| **Linux** 또는 **Windows**를 선택합니다.|
    |업데이트할 컴퓨터 |저장된 검색의 가져온 그룹을 선택하거나 드롭다운에서 머신을 선택하고 개별 머신을 선택합니다. **머신**을 선택한 경우 머신의 준비는 **업데이트 에이전트 준비** 열에 표시됩니다.</br> Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)을 참조하세요. |
    |업데이트 분류|필요한 모든 업데이트 분류를 선택합니다. CentOS는 기본적으로 이 기능을 지원하지 않습니다.|
    |제외할 업데이트|제외할 업데이트를 입력합니다. Windows의 경우 **KB** 접두사 없이 KB 문서를 입력합니다. Linux의 경우 패키지 이름을 입력하거나 와일드카드 문자를 사용합니다.  |
    |일정 설정|시작 시간을 선택하고 되풀이에 대해 **한 번** 또는 **정기**를 선택합니다. | 
    | 유지 관리 기간 |업데이트에 대해 설정되는 시간(분)입니다. 값은 30분 이상 6시간 이하여야 합니다. |
    | 다시 부팅 제어| 다시 부팅을 처리하는 방법을 결정합니다.</br>사용 가능한 옵션은 다음과 같습니다.</br>필요한 경우 다시 부팅(기본값)</br>항상 다시 부팅</br>다시 부팅 안 함</br>다시 부팅만 - 업데이트 설치 안 함|

4. 새로 만든 업데이트 배포의 상태를 보려면 **예약된 업데이트 배포**를 클릭합니다.

    ![새 업데이트 배포](media/migrate-oms-update-deployments/new-update-deployment.png)

5. 앞에서 설명한 대로, Azure Portal을 통해 새 배포를 구성한 후에는 Azure Portal에서 기존 배포를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Automation의 업데이트 관리에 대한 자세한 내용은 [업데이트 관리 개요](update-management/update-mgmt-overview.md)를 참조하세요.
