---
title: 템플릿 유효성 검사 도구를 사용 하 여 Azure Stack 용 템플릿 확인 | Microsoft Docs
description: Azure Stack 배포용 템플릿 확인
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 6bf84d7ecf2d436bdc00839699b150466b9de3ca
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247309"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>템플릿 유효성 검사 도구를 사용 하 여 Azure Stack에 대 한 템플릿을 확인합니다

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

템플릿 유효성 검사 도구를 사용 하 여 확인에 Azure Resource Manager [템플릿](azure-stack-arm-templates.md) Azure Stack에 배포 하기 위한 준비가 되었습니다. 템플릿 유효성 검사 도구는 Azure Stack 도구의 일부로 사용할 수 있습니다. 에 설명 된 단계를 사용 하 여 Azure Stack 도구를 다운로드 합니다 [GitHub에서 도구를 다운로드](azure-stack-powershell-download.md) 문서.

## <a name="overview"></a>개요

템플릿을 확인을 위해 클라우드 기능을 구축할 수 있는 첫 번째 파일을 다음 유효성 검사 도구를 실행 합니다. Azure Stack 도구에서 다음 PowerShell 모듈을 사용 하는 경우:

- 에 **CloudCapabilities** 폴더:<br>         `AzureRM.CloudCapabilities.psm1` 서비스 및 Azure Stack 클라우드에 버전을 나타내는 클라우드 기능 JSON 파일을 만듭니다.
- 에 **TemplateValidator** 폴더:<br>
`AzureRM.TemplateValidator.psm1` 클라우드 기능 JSON 파일을 사용 하 여 Azure Stack의 템플릿 배포에 대 한 테스트.

## <a name="build-the-cloud-capabilities-file"></a>클라우드 기능 파일 빌드

템플릿 유효성 검사기를 사용 하기 전에 실행 된 **AzureRM.CloudCapabilities** JSON 파일을 작성 하는 PowerShell 모듈.

>[!NOTE]
>통합된 시스템을 업데이트 하거나 새 서비스 또는 가상 확장을 추가 하는 경우이 모듈을 다시 실행 해야 있습니다.

1. Azure Stack에 연결 되어 있는지 확인 합니다. Azure Stack 개발 키트 호스트에서 이러한 단계를 수행할 수 있습니다 또는 사용할 수는 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 워크스테이션에서 연결 합니다.
2. 가져오기의 **AzureRM.CloudCapabilities** PowerShell 모듈:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. 사용 된 `Get-CloudCapabilities` 서비스 버전을 검색 하 고 클라우드 기능 JSON 파일을 만들 cmdlet. 지정 하지 않으면 **-OutputPath**, 파일 AzureCloudCapabilities.Json 현재 디렉터리에 만들어집니다. 사용자의 실제 위치를 사용 합니다.

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>템플릿 유효성 검사

템플릿을 사용 하 여 유효성을 검사 하려면 다음이 단계를 사용 합니다 **AzureRM.TemplateValidator** PowerShell 모듈. 사용자 고유의 템플릿을 사용 하거나 유효성을 검사 합니다 [Azure Stack 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다.

1. 가져오기의 **AzureRM.TemplateValidator.psm1** PowerShell 모듈:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. 템플릿 유효성 검사기를 실행 합니다.

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

템플릿 유효성 검사 경고 또는 오류는 원본 디렉터리에 HTML 파일과 PowerShell 콘솔에 기록 됩니다. 다음 화면 캡처에서는 유효성 검사 보고서의 예를 보여 줍니다.

![템플릿 유효성 검사 보고서](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>매개 변수

템플릿 유효성 검사기는 다음 매개 변수를 지원합니다.

| 매개 변수 | Description | 필수 |
| ----- | -----| ----- |
| TemplatePath | 재귀적으로 경로 Azure Resource Manager 템플릿의 찾을 지정합니다 | 예 | 
| TemplatePattern | 와 일치 하도록 템플릿 파일의 이름을 지정 합니다. | 아닙니다. |
| CapabilitiesPath | 클라우드 기능 JSON 파일의 경로를 지정합니다. | 예 | 
| IncludeComputeCapabilities | VM 크기 및 VM 확장 등의 IaaS 리소스의 평가 포함합니다. | 아닙니다. |
| IncludeStorageCapabilities | SKU 형식 같은 storage 리소스의 평가 포함합니다. | 아닙니다. |
| 보고서 | 생성 된 HTML 보고서의 이름을 지정합니다. | 아닙니다. |
| 자세한 정보 표시 | 콘솔에 오류 및 경고를 기록합니다. | 아닙니다.|

### <a name="examples"></a>예

이 예제에서는 모든 유효성을 검사 합니다 [Azure Stack 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) 로컬 저장소에 다운로드 합니다. 예제에서는 가상 머신 크기 및 Azure Stack 개발 키트 기능에 대 한 확장의 유효성도 검사 합니다.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>다음 단계

- [Azure Stack에 템플릿 배포](azure-stack-arm-templates.md)
- [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)
