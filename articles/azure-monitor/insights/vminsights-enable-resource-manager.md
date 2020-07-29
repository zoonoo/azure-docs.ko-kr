---
title: 리소스 관리자 템플릿을 사용 하 여 VM용 Azure Monitor 사용
description: 이 문서에서는 Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 하나 이상의 Azure 가상 머신 또는 가상 머신 확장 집합에 대해 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 89a9a1b762e02237a8ee08dca5d6eedefabaafbb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328230"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>리소스 관리자 템플릿을 사용 하 여 VM용 Azure Monitor 사용
이 문서에서는 리소스 관리자 템플릿을 사용 하 여 가상 머신 또는 가상 머신 확장 집합에 대 한 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다. 이 절차는 다음과 같은 경우에 사용할 수 있습니다.

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 컴퓨터

## <a name="prerequisites"></a>사전 요구 사항

- [Log Analytics 작업 영역을 만들고 구성](vminsights-configure-workspace.md)합니다. 
- 지원 되는 [운영 체제](vminsights-enable-overview.md#supported-operating-systems) 를 참조 하 여 활성화 하는 가상 머신 또는 가상 머신 확장 집합의 운영 체제가 지원 되는지 확인 합니다. 

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

가상 머신 및 가상 머신 확장 집합을 등록 하기 위한 예제 Azure Resource Manager 템플릿을 만들었습니다. 이러한 템플릿에는 기존 리소스에 대 한 모니터링을 사용 하도록 설정 하 고 모니터링을 사용 하는 새 리소스를 만드는 데 사용할 수 있는 시나리오가 포함 되어 있습니다.

>[!NOTE]
>템플릿을 사용 하도록 설정 하는 가상 머신 또는 가상 머신 확장 집합과 동일한 리소스 그룹에 템플릿을 배포 해야 합니다.


Azure Resource Manager 템플릿은 GitHub 리포지토리에서 [다운로드할](https://aka.ms/VmInsightsARMTemplates) 수 있는 보관 파일 (.zip)로 제공 됩니다. 파일의 내용에는 템플릿 및 매개 변수 파일을 사용 하 여 각 배포 시나리오를 나타내는 폴더가 포함 됩니다. 실행 하기 전에 매개 변수 파일을 수정 하 고 필요한 값을 지정 합니다. 

다운로드 파일에는 다양 한 시나리오에 대 한 다음 템플릿이 포함 되어 있습니다.

- **Existingvmonboarding** 템플릿을 사용 하면 가상 머신이 이미 있는 경우 VM용 Azure Monitor 수 있습니다.
- **Newvmonboarding** 템플릿은 가상 머신을 만들고 VM용 Azure Monitor 모니터링 하는 데 사용 됩니다.
- **Existingvmssonboarding 보 딩** 템플릿을 사용 하면 가상 머신 확장 집합이 이미 있는 경우 VM용 Azure Monitor 수 있습니다.
- **Newvmssonboarding** 템플릿은 가상 머신 확장 집합을 만들고 VM용 Azure Monitor를 모니터링 하는 데 사용 됩니다.
- **ConfigureWorkspace** 템플릿 Linux 및 Windows 운영 체제 성능 카운터의 솔루션 및 컬렉션을 사용 하도록 설정 하 여 VM용 Azure Monitor을 지원 하도록 Log Analytics 작업 영역을 구성 합니다.

>[!NOTE]
>가상 머신 확장 집합이 이미 있고 업그레이드 정책이 **수동**으로 설정 된 경우에는 **Existingvmssonboarding 보 딩** Azure Resource Manager 템플릿을 실행 한 후 기본적으로 인스턴스에 대해 VM용 Azure Monitor 사용 되지 않습니다. 인스턴스를 수동으로 업그레이드 해야 합니다.

## <a name="deploy-templates"></a>템플릿 배포
PowerShell 및 CLI를 사용 하는 다음 예제를 포함 하 여 [리소스 관리자 템플릿에 대 한 배포 방법을](../../azure-resource-manager/templates/deploy-powershell.md) 사용 하 여 템플릿을 배포할 수 있습니다.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>다음 단계

이제 가상 머신에 대 한 모니터링을 사용 하도록 설정 했으므로이 정보는 VM용 Azure Monitor 분석에 사용할 수 있습니다.

- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.

- VM의 성능에 대 한 병목 및 전반적인 사용률을 식별 하려면 [AZURE Vm 성능 보기](vminsights-performance.md)를 참조 하세요.
