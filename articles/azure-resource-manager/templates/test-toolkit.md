---
title: ARM 템플릿 테스트 도구 키트
description: 템플릿에서 ARM 템플릿 테스트 도구 키트를 실행 하는 방법을 설명 합니다. 도구 키트를 사용 하 여 권장 사례를 구현 했는지 확인할 수 있습니다.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255837"
---
# <a name="use-arm-template-test-toolkit"></a>ARM 템플릿 테스트 도구 키트 사용

[ARM 템플릿 테스트 도구 키트](https://aka.ms/arm-ttk) 는 템플릿에서 권장 사례를 사용 하는지 여부를 확인 합니다. 템플릿이 권장 사례를 준수 하지 않는 경우 제안 된 변경 내용과 함께 경고 목록을 반환 합니다. 테스트 도구 키트를 사용 하 여 템플릿 개발에서 일반적인 문제를 방지 하는 방법을 배울 수 있습니다.

테스트 도구 키트는 [일련의 기본 테스트](test-cases.md)를 제공 합니다. 이러한 테스트는 권장 사항 이지만 요구 사항은 아닙니다. 목표와 관련 된 테스트를 결정 하 고 실행 되는 테스트를 사용자 지정할 수 있습니다.

이 문서에서는 테스트 도구 키트를 실행 하는 방법 및 테스트를 추가 하거나 제거 하는 방법을 설명 합니다. 기본 테스트에 대 한 설명은 [toolkit 테스트 사례](test-cases.md)를 참조 하세요.

Toolkit은 PowerShell 또는 CLI의 명령에서 실행할 수 있는 PowerShell 스크립트 집합입니다.

## <a name="download-test-toolkit"></a>테스트 도구 키트 다운로드

테스트 도구 키트를 사용 하려면 스크립트를 포함 하는 [리포지토리](https://aka.ms/arm-ttk) 를 포크 및 복제 하거나 [최신 .zip 파일을 다운로드할](https://aka.ms/arm-ttk-latest)수 있습니다.

스크립트를 실행 하는 컴퓨터의 실행 정책에 따라 인터넷에서 스크립트를 실행 하는 것에 대 한 오류가 발생할 수 있습니다. [실행 정책을](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 변경 하거나 [스크립트 파일의 차단을 해제](/powershell/module/microsoft.powershell.utility/unblock-file)해야 합니다.

## <a name="run-on-powershell"></a>PowerShell에서 실행

테스트를 실행 하기 전에 모듈을 가져옵니다.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

**PowerShell**에서 테스트를 실행 하려면 다음 명령을 사용 합니다.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Linux에서 실행

테스트를 실행 하기 전에 [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux)를 설치 합니다.

Bash에서 **Linux** 에 대 한 테스트를 실행 하려면 다음 명령을 사용 합니다.

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

pwsh.exe에서 테스트를 실행할 수도 있습니다.

## <a name="run-on-macos"></a>MacOS에서 실행

테스트를 실행 하기 전에 [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos)를 설치 합니다. 

`coreutils` 설치:

```bash
brew install coreutils
```

**Macos**에서 테스트를 실행 하려면 다음 명령을 사용 합니다.

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>결과 형식

통과 하는 테스트는 **녹색** 및 앞에 **[+]** 로 표시 됩니다.

실패 한 테스트는 **빨강** 으로 표시 되 고 **[-]** 로 표시 됩니다.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="테스트 결과 보기":::

텍스트 결과는 다음과 같습니다.

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>테스트 매개 변수

**-Templatepath** 매개 변수를 제공 하는 경우 도구 키트는 해당 폴더에서 azuredeploy.json 또는 maintemplate.js된 템플릿을 찾습니다. 이 템플릿을 먼저 테스트 한 다음 폴더와 해당 하위 폴더에 있는 다른 모든 템플릿을 테스트 합니다. 다른 템플릿은 연결 된 템플릿으로 테스트 됩니다. 경로에 [CreateUiDefinition.js의](../managed-applications/create-uidefinition-overview.md)파일이 포함 된 경우 UI 정의와 관련 된 테스트를 실행 합니다.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

해당 폴더의 파일 하나를 테스트 하려면 **-file** 매개 변수를 추가 합니다. 그러나 폴더에는 여전히 azuredeploy.json 또는 maintemplate.js라는 주 템플릿이 있어야 합니다.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

기본적으로 모든 테스트가 실행 됩니다. 실행할 개별 테스트를 지정 하려면 **-Test** 매개 변수를 사용 합니다. 테스트 이름을 제공 합니다. 이름의 경우 [Toolkit 테스트 사례](test-cases.md)를 참조 하세요.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>테스트 사용자 지정

ARM 템플릿의 경우 도구 키트는 **\arm-ttk\testcases\deploymentTemplate**폴더의 모든 테스트를 실행 합니다. 테스트를 영구적으로 제거 하려면 폴더에서 해당 파일을 삭제 합니다.

[Createuidefinition](../managed-applications/create-uidefinition-overview.md) 파일의 경우 **\arm-ttk\testcases\CreateUiDefinition**폴더의 모든 테스트를 실행 합니다.

사용자 고유의 테스트를 추가 하려면 **Your-Custom-Test-Name.test.ps1**명명 규칙을 사용 하 여 파일을 만듭니다.

테스트는 개체 매개 변수 또는 문자열 매개 변수로 템플릿을 가져올 수 있습니다. 일반적으로는 둘 중 하나를 사용 하지만 둘 다 사용할 수 있습니다.

템플릿의 섹션을 가져오고 해당 속성을 반복 해야 하는 경우 개체 매개 변수를 사용 합니다. Object 매개 변수를 사용 하는 테스트는 다음과 같은 형식입니다.

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

템플릿 개체에는 다음과 같은 속성이 있습니다.

* $schema
* contentVersion
* 매개 변수
* variables
* 리소스
* outputs

예를 들어를 사용 하 여 매개 변수 컬렉션을 가져올 수 있습니다 `$TemplateObject.parameters` .

전체 템플릿에서 문자열 작업을 수행 해야 하는 경우 문자열 매개 변수를 사용 합니다. String 매개 변수를 사용 하는 테스트는 다음과 같은 형식입니다.

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

예를 들어 문자열 매개 변수의 정규식을 실행 하 여 특정 구문이 사용 되는지 확인할 수 있습니다.

테스트 구현에 대 한 자세한 내용을 보려면 해당 폴더의 다른 테스트를 살펴보세요.

## <a name="integrate-with-azure-pipelines"></a>Azure Pipelines와 통합

Azure 파이프라인에 테스트 도구 키트를 추가할 수 있습니다. 파이프라인을 사용 하면 템플릿이 업데이트 될 때마다 테스트를 실행 하거나 배포 프로세스의 일부로 실행할 수 있습니다.

테스트 도구 키트를 파이프라인에 추가 하는 가장 쉬운 방법은 타사 확장을 사용 하는 것입니다. 다음 두 가지 확장을 사용할 수 있습니다.

* [ARM TTK 테스트 실행](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM 템플릿 테스터](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

또는 사용자 고유의 작업을 구현할 수 있습니다. 다음 예제에서는 테스트 도구 키트를 다운로드 하는 방법을 보여 줍니다.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

다음 예제에서는 테스트를 실행 하는 방법을 보여 줍니다.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>다음 단계

기본 테스트에 대 한 자세한 내용은 [Toolkit 테스트 사례](test-cases.md)를 참조 하세요.
