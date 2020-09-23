---
title: CI/CD 도구를 사용 하 여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화
description: 이 문서에서는 Azure Stream Analytics CI/CD 도구를 사용 하 여 Azure Stream Analytics 프로젝트를 자동으로 빌드, 테스트 및 배포 하는 방법을 설명 합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: e772701396f172eaab906f99463bd9019728b531
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938037"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>CI/CD 도구를 사용 하 여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화

Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 Azure Stream Analytics Visual Studio Code 또는 Visual Studio 프로젝트를 자동으로 빌드, 테스트 및 배포할 수 있습니다. 개발 도구를 사용 하 여 프로젝트를 만들거나 기존 Stream Analytics 작업에서 프로젝트를 내보낼 수 있습니다. 이 문서에서는 CI/CD 시스템에서 npm 패키지를 사용 하는 방법을 설명 합니다. Azure Pipelines를 사용 하 여 배포 하 [는 경우 Azure DevOps를 사용 하 여 Stream Analytics 작업에 대 한 CI/CD 파이프라인 만들기](set-up-cicd-pipeline.md)를 참조 하세요.

## <a name="installation"></a>설치

[패키지를 직접 다운로드](https://www.npmjs.com/package/azure-streamanalytics-cicd) 하거나 명령을 사용 하 여 [전역적](https://docs.npmjs.com/downloading-and-installing-packages-globally) 으로 설치할 수 있습니다 `npm install -g azure-streamanalytics-cicd` . PowerShell 또는 **Azure Pipelines**의 빌드 파이프라인 Azure CLI 스크립트 태스크에서 사용할 수 있는 명령을 사용 하는 것이 좋습니다.

## <a name="build-the-project"></a>프로젝트 빌드

**Cicd** npm 패키지는 Stream Analytics [Visual Studio Code 프로젝트](quick-create-vs-code.md) 또는 [Visual Studio 프로젝트](stream-analytics-quick-create-vs.md)의 Azure Resource Manager 템플릿을 생성 하는 도구를 제공 합니다. Visual Studio Code 또는 Visual Studio를 설치 하지 않고 Windows, macOS 및 Linux에서 npm 패키지를 사용할 수도 있습니다.

패키지를 설치한 후에는 다음 명령을 사용 하 여 Stream Analytics 프로젝트를 빌드합니다.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

*빌드* 명령은 키워드 구문 검사를 수행 하 고 Azure Resource Manager 템플릿을 출력 합니다.

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트에 대 한 파일 ** 에 있는asaproj.js** 의 절대 경로 또는 Visual Studio 프로젝트용 **[your project name]. asaproj** . |
| `-outputPath` | Azure Resource Manager 템플릿에 대 한 출력 폴더의 경로입니다. 지정 하지 않으면 템플릿이 현재 디렉터리에 배치 됩니다. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Stream Analytics 프로젝트가 성공적으로 빌드되면 출력 폴더 아래에 다음 두 파일이 생성 됩니다.

* Azure Resource Manager 템플릿 파일

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager 매개 변수 파일

   `[ProjectName].JobTemplate.parameters.json`

parameters.js파일의 기본 매개 변수는 Visual Studio Code 또는 Visual Studio 프로젝트의 설정에서 가져온 것입니다. 다른 환경에 배포하려면 해당 매개 변수를 적절하게 바꾸면 됩니다.

모든 자격 증명의 기본값은 **null**입니다. Azure에 배포 하기 전에 값을 설정 해야 합니다.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Azure Data Lake Store Gen1의 관리 Id를 출력 싱크로 사용 하려면 Azure에 배포 하기 전에 PowerShell을 사용 하 여 서비스 주체에 대 한 액세스를 제공 해야 합니다. [Resource Manager 템플릿에서 관리 ID를 사용하여 ADLS Gen1을 배포](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)하는 방법을 자세히 알아봅니다.

## <a name="local-run"></a>로컬 실행

프로젝트에서 로컬 입력 파일을 지정한 경우 명령을 사용 하 여 Stream Analytics 스크립트를 로컬로 실행할 수 있습니다 `localrun` .

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 파일 ** 에** 대 한asaproj.js경로 이거나 Visual Studio 프로젝트용 **[your project name]. asaproj** 입니다. |
| `-outputPath` | 출력 폴더의 경로입니다. 지정 하지 않으면 출력 결과 파일이 현재 디렉터리에 배치 됩니다. |
| `-customCodeZipFilePath` | UDF 또는 역직렬 변환기와 같은 c # 사용자 지정 코드의 zip 파일 경로 (사용 되는 경우)입니다. Dll을 zip 파일로 패키지 하 고이 경로를 지정 합니다. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF는 Windows 에서만 작동 합니다.

## <a name="automated-test"></a>자동화 된 테스트

CI/CD npm 패키지를 사용 하 여 Stream Analytics 스크립트에 대해 자동화 된 테스트를 구성 하 고 실행할 수 있습니다.

### <a name="add-a-test-case"></a>테스트 사례 추가

테스트 사례는 테스트 구성 파일에 설명 되어 있습니다. 시작 하려면 명령을 사용 하 여 테스트 `addtestcase` 구성 파일에 테스트 사례 템플릿을 추가 합니다. 테스트 구성 파일이 없는 경우 기본적으로 생성 됩니다.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 파일 ** 에** 대 한asaproj.js경로 이거나 Visual Studio 프로젝트용 **[your project name]. asaproj** 입니다. |
| `-testConfigPath` | 테스트 구성 파일의 경로입니다. 이 파일을 지정 하지 않으면 파일의 현재 **asaproj.js** 디렉터리 아래에 있는 **\test** 에서 기본 파일 이름 **testConfig.js**을 사용 하 여 파일이 검색 됩니다. 존재 하지 않는 경우 새 파일이 만들어집니다. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

테스트 구성 파일이 비어 있는 경우 다음 내용이 파일에 기록 됩니다. 그렇지 않으면 테스트 사례가 **testcases**배열에 추가 됩니다. 필요한 입력 구성은 있는 경우 입력 구성 파일에 따라 자동으로 채워집니다. 그렇지 않으면 기본값이 구성 됩니다. 테스트를 실행 하기 전에 각 입력 및 예상 출력의 **FilePath** 를 지정 해야 합니다. 구성을 수동으로 수정할 수 있습니다.

테스트 유효성 검사에서 특정 출력을 무시 하도록 하려면 예상 되는 출력의 **필수** 필드를 **false**로 설정 합니다.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>단위 테스트 실행

다음 명령을 사용 하 여 프로젝트에 대 한 여러 테스트 사례를 실행할 수 있습니다. 출력 폴더에서 테스트 결과 요약이 생성 됩니다. 전달 된 모든 테스트에 대 한 코드 **0** 으로 프로세스가 종료 됩니다. 예외에 대해 **-1** 이 발생 했습니다. 테스트의 경우 **-2이 (가)** 실패 했습니다.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 파일 ** 에** 대 한asaproj.js경로 이거나 Visual Studio 프로젝트용 **[your project name]. asaproj** 입니다. |
| `-testConfigPath` | 테스트 구성 파일에 대 한 경로입니다. 이 파일을 지정 하지 않으면 파일의 현재 **asaproj.js** 디렉터리 아래에 있는 **\test** 에서 기본 파일 이름 **testConfig.js**을 사용 하 여 파일이 검색 됩니다.
| `-outputPath` | 테스트 결과 출력 폴더의 경로입니다. 지정 하지 않으면 출력 결과 파일이 현재 디렉터리에 배치 됩니다. |
| `-customCodeZipFilePath` | UDF 또는 역직렬 변환기와 같은 사용자 지정 코드의 zip 파일 경로 (사용 되는 경우)입니다. |

모든 테스트가 완료 되 면 JSON 형식의 테스트 결과 요약이 출력 폴더에 생성 됩니다. 요약 파일의 이름은 **testResultSummary.json**입니다.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Deploy to Azure

빌드에서 생성 된 Azure Resource Manager 템플릿 및 매개 변수 파일을 사용 하 여 [작업을 Azure에 배포할](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에 대 한 연속 통합 및 연속 배포](cicd-overview.md)
* [Azure Pipelines를 사용 하 여 Stream Analytics 작업에 대 한 CI/CD 파이프라인 설정](set-up-cicd-pipeline.md)
