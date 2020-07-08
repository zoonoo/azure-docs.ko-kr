---
title: Azure Stream Analytics 작업 Azure Resource Manager 템플릿 내보내기
description: 이 문서에서는 Azure Stream Analytics 작업에 대 한 Azure Resource Manager 템플릿을 내보내는 방법을 설명 합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 76c2cf7164c3e2d683a9294cb500ed05381811b6
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043532"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure Stream Analytics 작업 Azure Resource Manager 템플릿 내보내기

[Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 사용 하면 인프라를 코드로 구현할 수 있습니다. 템플릿은 리소스의 인프라 및 구성을 정의 하는 JSON (JavaScript Object Notation) 파일입니다. 배포할 리소스 및 해당 리소스에 대 한 속성을 지정 합니다.

Azure Resource Manager 템플릿을 내보내 Azure Stream Analytics 작업을 다시 배포할 수 있습니다.

## <a name="open-a-job-in-vs-code"></a>VS Code에서 작업 열기

템플릿을 내보내려면 먼저 Visual Studio Code에서 기존 Stream Analytics 작업을 열어야 합니다. 

작업을 로컬 프로젝트로 내보내려면 Azure Portal의 **Stream Analytics 탐색기** 에서 내보내려는 작업을 찾습니다. **쿼리** 페이지에서 **Visual Studio에서 열기**를 선택 합니다. 그런 다음 **Visual Studio Code**를 선택 합니다.

![Visual Studio Code에서 Stream Analytics 작업 열기](./media/resource-manager-export/open-job-vs-code.png)

Visual Studio Code를 사용 하 여 Stream Analytics 작업을 관리 하는 방법에 대 한 자세한 내용은 [Visual Studio Code 빠른](quick-create-vs-code.md)시작을 참조 하세요.

## <a name="compile-the-script"></a>스크립트 컴파일 

다음 단계는 작업 스크립트를 Azure Resource Manager 템플릿으로 컴파일하는 것입니다. 스크립트를 컴파일하기 전에 작업에 하나 이상의 입력과 하나의 출력이 구성 되어 있는지 확인 합니다. 입력 또는 출력이 구성 되지 않은 경우에는 입력 및 출력을 먼저 구성 해야 합니다.

1. Visual Studio Code에서 작업의 *script.asaql* 파일로 이동 합니다.

   ![Visual Studio Code script.asaql 파일](./media/resource-manager-export/transformation-asaql.png)

1. *Script.asaql* 파일을 마우스 오른쪽 단추로 클릭 하 고 메뉴에서 **Global.asa: Compile 스크립트** 를 선택 합니다.

1. Stream Analytics 작업 작업 영역에 **배포** 폴더가 표시 됩니다.

1. 배포에 사용 되는 Azure 리소스 관리 템플릿인 파일 *의JobTemplate.js* 를 탐색 합니다.

## <a name="complete-the-parameters-file"></a>매개 변수 파일을 완성 합니다.

다음으로, Azure 리소스 관리 템플릿 매개 변수 파일을 완성 합니다.

1. Visual Studio Code에서 Stream Analytics 작업 작업 영역의 **배포** 폴더에 있는 파일 *에서JobTemplate.parameters.js* 를 엽니다.

1. 입력 및 출력 키가 null입니다. Null 값을 입력 및 출력 리소스에 대 한 실제 액세스 키로 바꿉니다.

1. 매개 변수 파일을 저장합니다.

## <a name="deploy-using-templates"></a>템플릿을 사용 하 여 배포

이전 섹션에서 생성 한 Azure Resource Manager 템플릿을 사용 하 여 Azure Stream Analytics 작업을 배포할 준비가 되었습니다.

PowerShell 창에서 다음 명령을 실행 합니다. 실제 리소스 그룹 이름으로 *ResourceGroupName*, reaplce *파일*및 템플릿 *parameterfile* 을 하 고 작업 작업 영역의 **배포 폴더** 에 있는 파일에 대 한 전체 파일 *JobTemplate.js* 경로를 사용 하 여 *JobTemplate.parameters.js* 해야 합니다.

Azure PowerShell 구성 되지 않은 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)의 단계를 따르세요.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code를 사용 하 여 라이브 입력으로 Azure Stream Analytics 작업을 로컬로 테스트](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code를 사용 하 여 Azure Stream Analytics 작업 살펴보기 (미리 보기)](visual-studio-code-explore-jobs.md)