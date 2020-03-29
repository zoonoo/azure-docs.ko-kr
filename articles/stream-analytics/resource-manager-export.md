---
title: Azure 스트림 분석 작업 Azure 리소스 관리자 템플릿 내보내기
description: 이 문서에서는 Azure 스트림 분석 작업에 대한 Azure 리소스 관리자 템플릿을 내보내는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968925"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure 스트림 분석 작업 Azure 리소스 관리자 템플릿 내보내기

[Azure 리소스 관리자 템플릿을](../azure-resource-manager/templates/overview.md) 사용하면 인프라를 코드로 구현할 수 있습니다. 템플릿은 리소스에 대한 인프라 및 구성을 정의하는 JSON(JavaScript 개체 표기형) 파일입니다. 배포할 리소스와 해당 리소스에 대한 속성을 지정합니다.

Azure 리소스 관리자 템플릿을 내보내 Azure 스트림 분석 작업을 다시 배포할 수 있습니다.

## <a name="open-a-job-in-vs-code"></a>VS 코드에서 작업 열기

템플릿을 내보내기 전에 먼저 Visual Studio 코드에서 기존 스트림 분석 작업을 열어야 합니다. 

로컬 프로젝트로 작업을 내보내려면 Azure 포털의 검색 **분석 탐색기에서** 내보낼 작업을 찾습니다. **쿼리** 페이지에서 Visual **Studio에서 열기를**선택합니다. 그런 다음 **시각적 스튜디오 코드를**선택합니다.

![비주얼 스튜디오 코드에서 스트림 분석 작업 열기](./media/resource-manager-export/open-job-vs-code.png)

Visual Studio 코드를 사용하여 스트림 분석 작업을 관리하는 방법에 대한 자세한 내용은 [Visual Studio 코드 빠른 시작을](quick-create-vs-code.md)참조하십시오.

## <a name="compile-the-script"></a>스크립트 컴파일 

다음 단계는 작업 스크립트를 Azure 리소스 관리자 템플릿으로 컴파일하는 것입니다. 스크립트를 컴파일하기 전에 작업에 하나 이상의 입력과 하나의 출력이 구성되어 있는지 확인합니다. 입력 또는 출력이 구성되지 않은 경우 먼저 입력 및 출력을 구성해야 합니다.

1. 시각적 스튜디오 코드에서 작업의 *Transform.asaql* 파일로 이동합니다.

   ![비주얼 스튜디오 코드의 변환.asaql 파일](./media/resource-manager-export/transformation-asaql.png)

1. *Transform.asaql* 파일을 마우스 오른쪽 단추로 클릭하고 **메뉴에서 스크립트 컴파일을 ASA로** 선택합니다.

1. **배포** 폴더가 스트림 분석 작업 영역에 나타납니다.

1. 배포하는 데 사용되는 Azure 리소스 관리 템플릿인 *JobTemplate.json* 파일을 탐색합니다.

## <a name="complete-the-parameters-file"></a>매개 변수 파일 완료

다음으로 Azure 리소스 관리 템플릿 매개 변수 파일을 완료합니다.

1. Visual Studio 코드에서 스트림 분석 작업 영역의 **배포** 폴더에 있는 *JobTemplate.parameters.json* 파일을 엽니다.

1. 입력 및 출력 키는 null입니다. null 값을 입력 및 출력 리소스의 실제 액세스 키로 바꿉니다.

1. 매개 변수 파일을 저장합니다.

## <a name="deploy-using-templates"></a>템플릿을 사용하여 배포

이전 섹션에서 생성한 Azure 리소스 관리자 템플릿을 사용하여 Azure Stream Analytics 작업을 배포할 준비가 되었습니다.

PowerShell 창에서 다음 명령을 실행합니다. 리소스 *그룹 이름,* *TemplateFile*및 *TemplateParameterFile* 실제 리소스 그룹 이름으로 reaplce 해야 합니다., 그리고 작업 작업 영역의 **배포 폴더에** *JobTemplate.json* 및 *JobTemplate.parameters.json* 파일에 전체 파일 경로.

Azure PowerShell을 구성하지 않은 경우 [Azure PowerShell 설치 모듈의](https://docs.microsoft.com/powershell/azure/install-Az-ps)단계를 따릅니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>다음 단계

* [Visual Studio 코드를 사용하여 라이브 입력을 사용하여 로컬로 Azure Stream Analytics 작업 테스트](visual-studio-code-local-run-live-input.md)

* [비주얼 스튜디오 코드(미리 보기)를 통해 Azure 스트림 분석 작업 탐색](visual-studio-code-explore-jobs.md)