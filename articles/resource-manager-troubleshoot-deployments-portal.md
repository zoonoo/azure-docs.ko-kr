<properties
   pageTitle="포털을 통해 배포 작업 보기 | Microsoft Azure"
   description="Azure 포털을 사용하여 리소스 관리자 배포의 오류를 감지하는 방법에 대해 설명합니다."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Azure 포털을 통해 배포 작업 보기

> [AZURE.SELECTOR]
- [포털](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure에 리소스를 배포할 때 오류가 발생하는 경우 실행된 배포 작업에 대한 더욱 자세한 정보가 필요할 수 있습니다. Azure 포털은 쉽게 오류를 찾고 잠재적 해결 방법을 확인할 수 있는 인터페이스를 제공합니다.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## 감사 로그를 사용하여 문제 해결

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

배포 오류를 확인하려면 다음 단계를 사용합니다.

1. **찾아보기** 및 **감사 로그**를 선택하여 포털을 통해 감사 로그를 확인합니다.

    ![감사 로그 선택](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. **감사 로그** 블레이드에서 구독의 모든 리소스 그룹에 대한 최근 작업의 요약을 볼 수 있습니다. 여기에는 그래픽으로 나타낸 작업의 시간/상태와 작업 목록이 포함되어 있습니다.

    ![작업 표시](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. 목록에서 작업 중 하나를 선택할 수 있습니다. 조사하려는 오류가 포함된 작업을 선택합니다.

    ![작업 선택](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. 해당 작업에 대한 모든 이벤트를 표시합니다. 요약에서 **상관 관계 ID**를 확인합니다. 이 ID는 관련 이벤트를 추적하는 데 사용됩니다. 기술 지원과 함께 문제를 해결하면 유용할 수 있습니다. 이벤트에 대한 자세한 내용을 보려면 이벤트 중 하나를 선택하면 됩니다.

    ![이벤트 선택](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. 이벤트에 대한 세부 정보가 표시됩니다. 특히 오류에 대한 정보를 얻으려면 **속성**을 집중적으로 확인하세요.

    ![감사 로그 세부 정보 표시](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

감사 로그 보기를 필터링하여 특정 조건을 집중적으로 확인할 수 있습니다. 감사 로그 보기를 사용자 지정하려면

1. **감사 로그** 블레이드 위쪽의 **필터**를 선택합니다.

    ![로그 필터링](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. **필터** 블레이드에서 조건을 선택하여 원하는 작업만 표시하도록 감사 로그 보기를 제한할 수 있습니다. 예를 들어 특정 리소스 그룹에 대한 오류만 표시하도록 작업을 필터링할 수 있습니다.

    ![필터 옵션 설정](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. 시간 범위를 설정하여 작업을 좀 더 필터링할 수 있습니다. 다음 이미지는 보기를 특정 20분의 시간 범위로 필터링합니다.

    ![시간 설정](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

로그 감사 보기를 업데이트하면 지정된 조건을 충족하는 작업만 표시됩니다. 이러한 설정은 다음 번에 감사 로그를 볼 때도 그대로 유지되므로 작업 보기를 넓히려면 이러한 값을 변경해야 할 수 있습니다.

다행히도 배포가 실패한 이유를 확인할 수 있었습니다. 다음 섹션에 나와 있는 것처럼 배포 작업을 확인하여 상태 정보를 얻을 수도 있습니다.

## 배포 작업을 사용하여 문제 해결

배포 작업을 확인하려면 다음 단계를 사용합니다.

1. 배포에 관련된 리소스 그룹에 대해 마지막 배포의 상태를 확인합니다. 이 상태를 선택하여 자세한 내용을 확인할 수 있습니다.

    ![배포 상태](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. 최근 배포 기록이 표시됩니다. 실패한 배포를 선택합니다.

    ![배포 상태](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. 배포에 대한 정보를 확인하고 실패한 작업을 선택하여 오류에 대한 세부 정보를 봅니다.

    ![실패한 배포 보기](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. **작업 세부 정보** 블레이드에 실패한 작업에 대한 정보가 표시됩니다. 특히 상태 메시지를 잘 살펴봅니다.

    ![상태 메시지 보기](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## 다음 단계

- 특정 배포 오류에 대한 도움말은 [ Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
- 감사 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법을 알아보려면 [Resource Manager를 사용하여 작업 감사](resource-group-audit.md)를 참조하세요.
- 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0615_2016-->