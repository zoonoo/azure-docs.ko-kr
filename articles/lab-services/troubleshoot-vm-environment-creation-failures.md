---
title: VM 및 환경 오류 해결 Azure DevTest 랩
description: Azure DevTest 랩에서 가상 컴퓨터(VM) 및 환경 생성 실패 문제를 해결하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166341"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Azure DevTest 랩에서 가상 머신(VM) 및 환경 생성 실패 문제 해결
개발자 테스트 연구소는 컴퓨터 이름이 유효하지 않거나 랩 정책을 위반하려는 경우 경고를 제공합니다. 랩 VM `X` 또는 환경 상태 옆에 빨간색으로 표시되어 문제가 있음을 알립니다.  이 문서에서는 근본적인 문제를 찾는 데 사용할 수 있는 몇 가지 트릭을 제공 하며 나중에 문제를 방지 할 수 있습니다.

## <a name="portal-notifications"></a>포털 알림
Azure 포털을 사용하는 경우 가장 먼저 살펴볼 곳은 **알림 패널입니다.**  **벨 아이콘을**클릭하여 기본 명령 모음에서 사용할 수 있는 알림 패널에서는 랩 VM 또는 환경 생성이 성공했는지 여부를 알려줍니다.  오류가 발생한 경우 생성 실패와 관련된 오류 메시지가 표시됩니다. 세부 정보는 종종 문제를 해결하는 데 도움이 되는 추가 정보를 제공합니다. 다음 예제에서는 코어가 부족하여 가상 시스템 생성에 실패했습니다. 자세한 메시지는 문제를 해결하고 핵심 할당량 증가를 요청하는 방법을 알려줍니다.

![Azure 포털 알림](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>손상 상태의 VM
랩에서 VM 상태가 **손상된**것으로 표시되면 사용자가 가상 컴퓨터 페이지가 아닌 가상 **컴퓨터** 페이지에서 이동할 수 있는 **기본** VM이 삭제되었을 수 있습니다. 실험실에서 VM을 삭제하여 DevTest 랩에서 랩을 정리합니다. 그런 다음 랩에서 VM을 다시 만듭니다. 

![손상된 상태의 VM](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>활동 로그
VM 또는 환경을 만든 후 언젠가 오류를 조사하는 경우 활동 로그를 확인합니다. 이 섹션에서는 VM 및 환경에 대한 로그를 찾는 방법을 보여 줍니다.

## <a name="activity-logs-for-virtual-machines"></a>가상 시스템에 대한 활동 로그

1. 랩의 홈 페이지에서 VM을 선택하여 **가상 컴퓨터** 페이지를 시작합니다.
2. 가상 **컴퓨터** 페이지에서 왼쪽 메뉴의 **모니터링** 섹션에서 **활동 로그를** 선택하여 VM과 연결된 모든 로그를 확인합니다.
3. 활동 로그 항목에서 실패한 작업을 선택합니다. 일반적으로 실패한 작업을 이라고 `Write Virtualmachines`합니다.
4. 오른쪽 창에서 JSON 탭으로 전환합니다. 로그의 JSON 보기에서 세부 정보를 볼 수 있습니다.

    ![VM에 대한 활동 로그](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 속성을 찾을 때까지 JSON 로그를 살펴봅니다. `statusMessage` 해당하는 경우 기본 오류 메시지와 자세한 정보를 제공합니다. 다음 JSON은 이 문서의 앞에서 볼 수 있는 초과 된 오류를 인용 하는 코어에 대 한 예입니다.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>환경에 대한 활동 로그

환경 만들기에 대한 활동 로그를 보려면 다음 단계를 따르십시오.

1. 랩의 홈 페이지에서 왼쪽 메뉴에서 **구성 및 정책을** 선택합니다.
2. 구성 **및 정책** 페이지에서 메뉴에서 **활동 로그를 선택합니다.**
3. 로그의 활동 목록에서 실패를 찾아 선택합니다.
4. 오른쪽 창에서 JSON 탭으로 전환하고 **상태 메시지를**찾습니다.

    ![환경 활동 로그](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>리소스 관리자 템플릿 배포 로그
사용자 환경 또는 가상 시스템이 자동화를 통해 생성된 경우 오류 정보를 마지막으로 살펴볼 수 있는 곳이 하나 있습니다. Azure 리소스 관리자 템플릿 배포 로그입니다. 자동화를 통해 랩 리소스를 만들 때 Azure 리소스 관리자 템플릿 배포를 통해 수행되는 경우가 많습니다. DevTest 랩 리소스를 만드는 샘플 Azure 리소스 관리자 템플릿을 참조하세요.[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)

랩 템플릿 배포 로그를 보려면 다음 단계를 따르십시오.

1. 랩이 있는 리소스 그룹에 대한 페이지를 시작합니다.
2. **설정**에서 왼쪽 메뉴에서 **배포를 선택합니다.**
3. 실패한 상태가 있는 배포를 찾아 서 선택합니다.
4. **배포** 페이지에서 실패한 **작업에** 대한 작업 세부 정보 링크를 선택합니다.
5. **작업 세부 정보** 창에서 실패한 작업에 대한 세부 정보가 표시됩니다.

## <a name="next-steps"></a>다음 단계
[아티팩트 오류 해결](devtest-lab-troubleshoot-artifact-failure.md) 참조
