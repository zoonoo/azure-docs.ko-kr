---
title: VM 및 환경 오류 문제 해결 Azure DevTest Labs
description: Azure DevTest Labs에서 VM (가상 머신) 및 환경 만들기 오류 문제를 해결 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476481"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Azure DevTest Labs에서 VM (가상 머신) 및 환경 만들기 오류 문제 해결
DevTest Labs는 컴퓨터 이름이 잘못 되었거나 랩 정책을 위반 하는 경우 경고를 제공 합니다. 경우에 따라 문제가 `X` 발생 했음을 알리는 랩 VM 또는 환경 상태 옆에 빨간색이 표시 됩니다.  이 문서에서는 기본 문제를 찾고 나중에 문제를 방지 하는 데 사용할 수 있는 몇 가지 트릭을 제공 합니다.

## <a name="portal-notifications"></a>포털 알림
Azure Portal 사용 하는 경우에는 **알림 패널이**가장 먼저 표시 되는 위치입니다.  주 명령 모음에서 **종 모양 아이콘**을 클릭 하 여 사용할 수 있는 알림 패널은 랩 VM 또는 환경을 만드는 데 성공 했는지 여부를 알려 줍니다.  오류가 발생 한 경우 생성 오류와 관련 된 오류 메시지가 표시 됩니다. 이 정보는 문제를 해결 하는 데 도움이 되는 추가 정보를 제공 하는 경우가 많습니다. 다음 예제에서는 코어 부족으로 인해 가상 컴퓨터를 만들지 못했습니다. 자세한 메시지는 문제를 해결 하 고 코어 할당량 증가를 요청 하는 방법을 알려줍니다.

![Azure Portal 알림](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>손상 상태의 VM
랩의 VM 상태가 **손상**됨으로 표시 되는 경우 사용자가 DevTest Labs 페이지가 아닌 **Virtual Machines** 페이지에서 탐색할 수 있는 **가상 컴퓨터** 페이지에서 기본 vm이 삭제 되었을 수 있습니다. 랩에서 VM을 삭제 하 여 DevTest Labs에서 랩을 정리 합니다. 그런 다음 랩에서 VM을 다시 만듭니다. 

![VM이 손상 된 상태](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>활동 로그
VM 또는 환경의 생성을 시도한 후에 실패를 조사 하는 경우 활동 로그를 확인 합니다. 이 섹션에서는 Vm 및 환경에 대 한 로그를 찾는 방법을 보여 줍니다.

## <a name="activity-logs-for-virtual-machines"></a>가상 컴퓨터에 대 한 활동 로그

1. 랩의 홈 페이지에서 VM을 선택 하 여 **가상 머신** 페이지를 시작 합니다.
2. **가상 컴퓨터** 페이지의 왼쪽 메뉴에 있는 **모니터링** 섹션에서 **작업 로그** 를 선택 하 여 VM에 연결 된 모든 로그를 확인 합니다.
3. 활동 로그 항목에서 실패 한 작업을 선택 합니다. 일반적으로 실패 한 작업이 호출 됩니다 `Write Virtualmachines` .
4. 오른쪽 창에서 JSON 탭으로 전환 합니다. 로그의 JSON 뷰에 세부 정보가 표시 됩니다.

    ![VM에 대 한 활동 로그](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 속성을 찾을 때까지 JSON 로그를 확인 합니다 `statusMessage` . 기본 오류 메시지와 자세한 정보 (해당 하는 경우)를 제공 합니다. 다음 JSON은이 문서의 앞부분에서 볼 수 있는 핵심 따옴표 초과 오류에 대 한 예입니다.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>환경에 대 한 활동 로그

환경 만들기에 대 한 활동 로그를 보려면 다음 단계를 수행 합니다.

1. 랩의 홈 페이지에 있는 왼쪽 메뉴에서 **구성 및 정책** 을 선택 합니다.
2. **구성 및 정책** 페이지의 메뉴에서 **활동 로그** 를 선택 합니다.
3. 로그의 작업 목록에서 오류를 찾아 선택 합니다.
4. 오른쪽 창에서 JSON 탭으로 전환 하 고 **Statusmessage**를 찾습니다.

    ![환경 활동 로그](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>리소스 관리자 템플릿 배포 로그
환경 또는 가상 컴퓨터가 자동화를 통해 만들어진 경우 오류 정보를 찾는 마지막 위치가 있습니다. Azure Resource Manager 템플릿 배포 로그입니다. 자동화를 통해 랩 리소스를 만들 때 Azure Resource Manager 템플릿 배포를 통해 수행 되는 경우가 많습니다. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)DevTest Labs 리소스를 만드는 샘플 Azure Resource Manager 템플릿은를 참조 하세요.

랩 템플릿 배포 로그를 보려면 다음 단계를 수행 합니다.

1. 랩을 존재 하는 리소스 그룹에 대 한 페이지를 시작 합니다.
2. **설정**아래의 왼쪽 메뉴에서 **배포** 를 선택 합니다.
3. 실패 상태의 배포를 찾아 선택 합니다.
4. **배포** 페이지에서 실패 한 작업에 대 한 **작업 세부 정보** 링크를 선택 합니다.
5. **작업 세부 정보** 창에서 실패 한 작업에 대 한 세부 정보가 표시 됩니다.

## <a name="next-steps"></a>다음 단계
[아티팩트 오류 문제 해결](devtest-lab-troubleshoot-artifact-failure.md) 을 참조 하세요.
