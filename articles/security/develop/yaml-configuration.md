---
title: Microsoft Azure Security Code Analysis 작업 사용자 지정 가이드
description: 이 문서에서는 Microsoft Security Code Analysis 확장에서 모든 작업을 사용자 지정하기 위한 YAML 구성 옵션을 설명합니다.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 9ea54329328725f5ae74525b63daae060289773e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115980"
---
# <a name="yaml-configuration-options-to-customize-the-build-tasks"></a>빌드 작업을 사용자 지정하는 YAML 구성 옵션

> [!Note]
> 2022년 3월 1일부터 MSCA(Microsoft Security Code Analysis) 확장이 사용 중지됩니다. 기존 MSCA 고객은 2022년 3월 1일까지 MSCA에 대한 액세스가 유지됩니다. Azure DevOps의 대체 옵션은 [OWASP 소스 코드 분석 도구](https://owasp.org/www-community/Source_Code_Analysis_Tools)를 참조하세요. GitHub로 마이그레이션을 계획하는 고객의 경우 [GitHub 고급 보안](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)을 확인할 수 있습니다.

이 문서에서는 각 빌드 작업에서 사용할 수 있는 모든 YAML 구성 옵션을 나열합니다. 이 문서는 보안 코드 분석 도구에 대한 작업으로 시작하여 후처리 작업으로 끝납니다.

## <a name="anti-malware-scanner-task"></a>맬웨어 방지 스캐너 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | pickList | always | True | Basic | Basic, Custom | 
| ScanType | pickList | InputType = Basic | True | CustomScan | CustomScan, FullSystemScan, QuickScan, YourConfiguredScan | 맬웨어 방지 검색에 사용할 검색 유형입니다.
| FileDirPath | filePath | ScanType = CustomScan | True | $(Build.StagingDirectory) |  | 검색할 파일 또는 디렉터리를 나타냅니다.
| DisableRemediation | boolean | ScanType = CustomScan | False | true |  | 선택된 경우: 1) 파일 제외가 무시됩니다. 2) 보관 파일이 검색됩니다. 3) 검사 후 작업이 적용되지 않습니다. 4) 검사 후 이벤트 로그 항목이 기록되지 않습니다. 5) 사용자 지정 검색의 검색 항목이 사용자 인터페이스에 표시되지 않습니다. 6) 콘솔 출력에 사용자 지정 검색의 검색 항목 목록이 표시됩니다.
| BootSectorScan | boolean | ScanType = CustomScan | False | false |  | 선택하는 경우 부팅 섹터 검색을 사용하도록 설정합니다.
| 인수 | 문자열 | InputType = Custom | True | -Scan -ScanType 3 -DisableRemediation -File $(Build.StagingDirectory) |  | 명령줄 인수입니다. 여기서 -File에 대한 인수는 절대 경로이거나 빌드 에이전트에 미리 정의된 $(Build.StagingDirectory)에 대한 상대 경로입니다. 참고: -File에 대한 인수를 마지막 인수로 제공하지 않으면 기본값으로 $(Build.StagingDirectory)가 설정됩니다. MpCmdRun.exe 도구에서 허용하는 고유한 인수를 제공할 수도 있습니다.<br/><br/>이 도구의 명령줄 인수에 대한 자세한 내용을 보려면 인수 필드에 <strong>-h</strong> 또는 <strong>-?</strong>를 입력하고 빌드 작업을 실행하세요.
| EnableServices | boolean | always | True | false |  | 선택하는 경우 Windows Update에 필요한 서비스를 사용하도록 설정합니다(설정되지 않은 경우).<br/>**참고**: 그룹 정책에서 서비스를 사용하도록 설정했는지, 이 빌드를 실행하는 계정에 관리자 권한이 있는지 확인하세요.
| SupportLogOnError | boolean | always | True | false |  | 선택하는 경우 오류가 발생했을 때 진단을 위한 지원 파일을 수집합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.<br/>**참고**: 이 빌드가 실행되는 계정에 관리자 권한이 있는지 확인하세요.
| TreatSignatureUpdateFailureAs | pickList | always | True | 경고 | Error, Standard, Warning | 런타임에 서명을 업데이트할 수 없는 경우 사용되는 로그 수준입니다. **Error** 로 설정하는 경우 서명 업데이트에 실패하면 빌드 작업이 실패합니다. 서명이 상대적으로 최신(3시간 미만)인 경우에도 호스트된 빌드 에이전트에서 서명 업데이트가 실패하는 것이 일반적입니다.
| SignatureFreshness | pickList | always | True | UpToDate | OneDay, ThreeDays, TwoDays, UpToDate | 맬웨어 방지 서명에 허용되는 최대 경과 기간입니다. 서명이 업데이트될 수 없고 이 값보다 오래된 경우 빌드 작업은 **서명 경과 기간 확인** 필드에서 선택한 값에 따라 동작합니다. 참고: **Up-To-Date** 를 선택하는 경우 서명은 최대 3시간까지 허용됩니다.
| TreatStaleSignatureAs | pickList | always | True | Error | Error, Standard, Warning | 서명 경과 기간이 선택한 **맬웨어 방지 서명 경과 기간** 보다 오래된 경우 사용되는 로그 수준입니다. 맬웨어 방지 검색을 계속하도록 오래된 서명을 **경고** 또는 **정보 제공** 으로 처리할 수 있지만 이 방법은 사용하지 않는 것이 좋습니다.

## <a name="binskim-task"></a>BinSkim 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | pickList | always | True | Basic | Basic, CommandLine | 
| 인수 | 문자열 | InputType = CommandLine | True |  |  | 실행할 표준 BinSkim 명령줄 인수입니다. 출력 경로가 제거되고 바뀝니다.<br>이 도구의 명령줄 인수에 대한 자세한 내용을 보려면 인수 필드에 **help** 를 입력하고 빌드 작업을 실행하세요.
| 함수 | pickList | InputType = Basic | True | 분석 | analyze, dump, exportConfig, exportRules | 
| AnalyzeTarget | filePath | InputType = Basic && Function = analyze | True | $(Build.ArtifactStagingDirectory)\*.dll,<br>$(Build.ArtifactStagingDirectory)\*.exe |  | 분석할 하나 이상의 이진 파일로 확인되는 파일, 디렉터리 또는 필터 패턴에 대한 하나 이상의 지정자입니다. (';'로 구분된 목록)
| AnalyzeSymPath | 문자열 | InputType = Basic && Function = analyze | False |  |  | 대상에 대한 기호 파일의 경로입니다.
| AnalyzeConfigPath | 문자열 | InputType = Basic && Function = analyze | False | default |  | 분석을 구성하는 데 사용될 정책 파일의 경로입니다. 기본 제공 설정을 사용하려면 'default' 값을 전달합니다.
| AnalyzePluginPath | 문자열 | InputType = Basic && Function = analyze | False |  |  | 분석 집합의 모든 대상에 대해 호출되는 플러그 인의 경로입니다.
| AnalyzeRecurse | boolean | InputType = Basic && Function = analyze | False | true |  | 파일 지정자 인수를 평가할 때 하위 디렉터리로 재귀됩니다.
| AnalyzeVerbose | boolean | InputType = Basic && Function = analyze | False | false |  | 자세한 정보 출력을 내보냅니다. 결과로 생성되는 포괄적인 보고서는 규정 준수 시나리오에 대한 적절한 증거를 제공하도록 설계되었습니다.
| AnalyzeHashes | boolean | InputType = Basic && Function = analyze | False | false |  | SARIF 보고서를 내보낼 때 분석 대상의 SHA-256 해시를 출력합니다.
| AnalyzeStatistics | boolean | InputType = Basic && Function = analyze | False | false |  | 분석 세션에 대한 타이밍 및 기타 통계를 생성합니다.
| AnalyzeEnvironment | boolean | InputType = Basic && Function = analyze | False | false |  | 실행의 컴퓨터 환경 세부 정보를 출력 파일에 기록합니다. 경고: 이 옵션은 모든 환경 변수 값과 같은 잠재적으로 중요한 정보를 내보낸 로그에 기록합니다.
| ExportRulesOutputType | pickList | InputType = Basic && Function = exportRules | False | SARIF | SARIF, SonarQube | 출력할 규칙 설명자 파일의 유형입니다. 이는 Publish Security Analysis Logs 빌드 작업에서 게시한 BinSkim logs 폴더에 포함됩니다.
| DumpTarget | filePath | InputType = Basic && Function = dump | True | $(Build.ArtifactStagingDirectory) |  | 분석할 하나 이상의 이진 파일로 확인되는 파일, 디렉터리 또는 필터 패턴에 대한 하나 이상의 지정자입니다. (';'로 구분된 목록)
| DumpRecurse | boolean | InputType = Basic && Function = dump | False | true |  | 파일 지정자 인수를 평가할 때 하위 디렉터리로 재귀됩니다.
| DumpVerbose | boolean | InputType = Basic && Function = dump | False | true |  | 자세한 정보 출력을 내보냅니다. 결과로 생성되는 포괄적인 보고서는 규정 준수 시나리오에 대한 적절한 증거를 제공하도록 설계되었습니다.
| toolVersion | pickList | always | False | 최신 | 1.5.0, Latest, LatestPreRelease | 실행할 도구의 버전입니다.

## <a name="credential-scanner-task"></a>Credential Scanner 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| outputFormat | pickList | always | False | pre | csv, pre, tsv | Credential Scanner 결과 파일의 출력 형식입니다.
| toolVersion | pickList | always | False | 최신 | 1.27.7, Latest, LatestPreRelease | 실행할 도구의 버전입니다.
| scanFolder | filePath | always | False | $(Build.SourcesDirectory) |  | 자격 증명을 검색할 리포지토리의 폴더입니다.
| searchersFileType | pickList | always | False | 기본값 | Custom, Default, DefaultAndCustom | 검색에 사용되는 searchers 파일을 찾기 위한 옵션입니다.
| searchersFile | filePath | searchersFileType == Custom OR searchersFileType == DefaultAndCustom | False |  |  | 실행할 검사의 Credential Scanner searchers 구성 파일입니다. Credential Scanner searchers 파일에 대한 쉼표로 구분된 경로 목록을 제공하여 여러 값을 포함하고 사용할 수 있습니다.
| suppressionsFile | filePath | always | False |  |  | 출력 로그에서 문제를 표시하지 않기 위해 사용할 Credential Scanner 비표시 파일입니다.
| suppressAsError | boolean | always | False | false |  | 비표시 일치 항목은 기본 비표시 출력 파일 [-O]-suppressed.[-f]가 아니라 출력 파일 [-O]-matches.[-f]로 출력됩니다. (기본값은 'False')
| verboseOutput | boolean | always | False | false |  | 자세한 정보를 출력합니다.
| batchSize | 문자열 | always | False |  |  | Credential Scanner를 병렬로 실행하는 데 사용되는 동시 스레드 수입니다. (기본값은 20)<br/>값은 1에서 2147483647 사이의 범위 내에 있어야 합니다.
| regexMatchTimeoutInSeconds | 문자열 | always | False |  |  | 검사를 중단하기 전에 검색자 일치를 시도하는 데 소요되는 시간(초)입니다.<br/>``-Co RegexMatchTimeoutInSeconds=<Value>``를 명령줄에 추가합니다.
| fileScanReadBufferSize | 문자열 | always | False |  |  | 콘텐츠를 읽는 동안 버퍼 크기(바이트)입니다. (기본값은 524288)<br/>``-Co FileScanReadBufferSize=<Value>``를 명령줄에 추가합니다.
| maxFileScanReadBytes | 문자열 | always | False |  |  | 콘텐츠 분석 중 지정된 파일에서 읽을 최대 바이트 수입니다. (기본값은 104857600)<br/>``-Co MaxFileScanReadBytes=<Value>``를 명령줄에 추가합니다.

## <a name="roslyn-analyzers-task"></a>Roslyn Analyzers 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                   |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userProvideBuildInfo | pickList | always | True | auto | auto, msBuildInfo | 사용자가 Roslyn 분석을 위한 MSBuild 버전, MSBuild 아키텍처 및 명령줄 빌드를 제공하기 위한 옵션입니다. **자동** 을 선택하는 경우 이 작업은 동일한 파이프라인의 이전 **MSBuild**, **VSBuild** 및/또는 **.NET Core**(빌드의 경우) 작업에서 빌드 정보를 검색합니다.
| msBuildVersion | pickList | userProvideBuildInfo == msBuildInfo | True | 16.0 | 15.0, 16.0 | MSBuild 버전입니다.
| msBuildArchitecture | pickList | userProvideBuildInfo == msBuildInfo | True | x86 | DotNetCore, x64, x86 | MSBuild 아키텍처입니다. 참고: 빌드 명령줄이 **dotnet.exe 빌드** 를 호출하는 경우 **Via .NET Core** 옵션을 선택합니다.
| msBuildCommandline | 문자열 | userProvideBuildInfo == msBuildInfo | True |  |  | 솔루션 또는 프로젝트를 컴파일하기 위한 전체 빌드 명령줄입니다.<br/><br/>참고: 명령줄은 **MSBuild.exe** 또는 **dotnet.exe** 에 대한 전체 경로로 시작해야 합니다.<br/>명령은 $(Build.SourcesDirectory)를 작업 디렉터리로 사용하여 실행됩니다.
| rulesetName | pickList | always | False | 권장 | Custom, None, Recommended, Required | 사용할 명명된 규칙 집합입니다.<br/><br/>`Ruleset Configured In Your Visual Studio Project File(s)`를 선택하는 경우 VS 프로젝트 파일에 미리 구성된 규칙 집합이 사용됩니다. `Custom`을 선택하는 경우 사용자 지정 규칙 집합 경로 옵션을 설정할 수 있습니다.
| rulesetVersion | pickList | rulesetName == Required OR rulesetName == Recommended | False | 최신 | 8.0, 8.1, 8.2, Latest, LatestPreRelease | 선택한 SDL 규칙 집합의 버전입니다.
| customRuleset | 문자열 | rulesetName = Custom | False |  |  | 사용할 규칙 집합에 액세스할 수 있는 경로입니다. 상대 경로는 원본 리포지토리(`$(Build.SourcesDirectory)`) 루트로 정규화됩니다.<br/><br/>규칙 집합이 `Rules`를 지정하고 `Actions`가 `Error`로 설정되면 빌드 작업이 실패합니다. 이를 수행하는 규칙 집합을 사용하려면 빌드 작업의 `Control Options`에서 `Continue on error`를 선택합니다.
| microsoftAnalyzersVersion | pickList | always | False | 최신 | 2.9.3, 2.9.4, 2.9.6, Latest, LatestPreRelease | 실행할 [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 패키지의 버전입니다.
| suppressionFileForCompilerWarnings | filePath | always | False |  |  | C# 및 VB 컴파일러 경고를 표시하지 않는 비표시 파일입니다.<br/><br/>각 경고 ID가 한 줄에 하나씩 나열된 일반 텍스트 파일입니다.<br/>컴파일러 경고의 경우 경고 식별자의 숫자 부분만 지정하면 됩니다. 예를 들어 1018은 CS1018을 표시하지 않고 CA1501는 CA1501을 표시하지 않습니다.<br/><br/>상대 파일 경로는 원본 리포지토리(`$(Build.SourcesDirectory)`)의 루트에 추가됩니다.

## <a name="tslint-task"></a>TSLint 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RuleLibrary | pickList | always | True | tslint | custom, microsoft, tslint | 모든 결과는 선택된 버전의 TSLint(**Base Only**)와 함께 제공되는 규칙을 포함합니다.<br/><br/>**Base Only -** TSLint와 함께 제공되는 규칙만 포함합니다.<br/><br/>**Include Microsoft Rules -** [tslint-microsoft-contrib](https://github.com/Microsoft/tslint-microsoft-contrib)를 다운로드하고 TSLint 실행에 사용할 수 있는 규칙을 포함합니다. 이 옵션을 선택하면 Microsoft의 규칙에 필요하므로 자동으로 사용되는 `Type Checking` 확인란은 숨겨집니다. 또한 `Microsoft Contribution Version` 필드가 표시되어 [npm](https://www.npmjs.com/package/tslint-microsoft-contrib)의 `tslint-microsoft-contrib` 버전을 선택할 수 있습니다.<br/><br/>**Include Custom Rules -** TSLint 실행에 사용할 수 있는 TSLint 규칙의 디렉터리에 액세스할 수 있는 경로를 입력하는 `Rules Directory` 필드가 표시됩니다.<br/><br/>**참고:** 많은 사용자가 Microsoft 규칙 집합을 구성하는 데 문제를 겪기 때문에 기본값이 tslint로 변경되었습니다. 특정 버전 구성에 대한 자세한 내용은 [GitHub의 tslint-microsoft-contrib](https://github.com/microsoft/tslint-microsoft-contrib)를 참조하세요.
| RulesDirectory | 문자열 | RuleLibrary == custom | True |  |  | TSLint 실행에서 사용할 수 있는 추가 TSLint 규칙을 포함하는 액세스 가능한 디렉터리입니다.
| Ruleset | pickList | RuleLibrary != microsoft | True | tsrecommended | custom, tslatest, tsrecommended | TypeScript 파일에 대해 실행할 규칙을 정의합니다.<br/><br/>**[tslint:latest](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) -** `tslint:recommended`를 확장하며 모든 TSLint 릴리스의 최신 규칙에 대한 구성을 포함하도록 지속적으로 업데이트됩니다. 이 구성을 사용하면 코드에서 lint 오류를 유발하는 새 규칙이 활성화되므로 부 릴리스 간에 호환성이 손상되는 변경이 발생할 수 있습니다. TSLint가 주 버전 범프에 도달하면 `tslint:recommended`가 `tslint:latest`와 동일하게 업데이트됩니다.<br/><br/>**[tslint:recommended](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) -** TSLint 사용 시 TypeScript 프로그래밍에 권장되는 약간 독자적인 안정된 규칙 집합입니다. 이 구성은 `semver`를 따르며, 따라서 부 릴리스 또는 패치 릴리스 간에 호환성이 손상되는 변경이 없습니다.
| RulesetMicrosoft | pickList | RuleLibrary == microsoft | True | mssdlrequired | custom, msrecommended, mssdlrecommended, mssdlrequired, tslatest, tsrecommended | TypeScript 파일에 대해 실행할 규칙을 정의합니다.<br/><br/>**[microsoft:sdl-required](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -** tslint에서 제공하는 사용 가능한 모든 검사를 실행하고 '필수' [SDL(보안 개발 수명 주기)](https://www.microsoft.com/sdl/) 정책을 충족하는 tslint-microsoft-contrib 규칙을 실행합니다.<br/><br/>**[microsoft:sdl-recommended](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -** tslint에서 제공하는 사용 가능한 모든 검사를 실행하고 '필수 및 권장' [SDL(보안 개발 수명 주기)](https://www.microsoft.com/sdl/) 정책을 충족하는 tslint-microsoft-contrib 규칙을 실행합니다.<br/><br/>**microsoft:recommended** tslint-microsoft-contrib 규칙의 작성자가 권장하는 모든 검사입니다. 여기에는 보안 및 비보안 검사가 포함됩니다.<br/><br/>**[tslint:latest](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) -** `tslint:recommended`를 확장하며 모든 TSLint 릴리스의 최신 규칙에 대한 구성을 포함하도록 지속적으로 업데이트됩니다. 이 구성을 사용하면 코드에서 lint 오류를 유발하는 새 규칙이 활성화되므로 부 릴리스 간에 호환성이 손상되는 변경이 발생할 수 있습니다. TSLint가 주 버전 범프에 도달하면 `tslint:recommended`가 `tslint:latest`와 동일하게 업데이트됩니다.<br/><br/>**[tslint:recommended](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) -** TSLint 사용 시 TypeScript 프로그래밍에 권장되는 약간 독자적인 안정된 규칙 집합입니다. 이 구성은 `semver`를 따르며, 따라서 부 릴리스 또는 패치 릴리스 간에 호환성이 손상되는 변경이 없습니다.
| RulesetFile | 문자열 | Ruleset == custom OR RulesetMicrosoft == custom | True |  |  | 실행할 규칙을 지정하는 [구성 파일](https://palantir.github.io/tslint/usage/cli/)입니다.<br/><br/>구성에 대한 경로는 [사용자 지정 규칙](https://palantir.github.io/tslint/develop/custom-rules/)에 대한 경로로 추가됩니다.
| FileSelectionType | pickList | always | True | fileGlob | fileGlob, projectFile | 
| 파일 | 문자열 | FileSelectionType == fileGlob | True | **\*.ts |  | 처리할 파일을 결정하는 파일 [glob](https://www.npmjs.com/package/glob)입니다. 경로는 `Build.SourcesDirectory` 값을 기준으로 상대적입니다.<br/><br/>Microsoft의 기여 라이브러리를 사용하려면 프로젝트 파일을 사용해야 합니다. Microsoft의 기여 라이브러리를 `File Glob Pattern` 옵션과 함께 사용하는 경우 프로젝트 파일이 자동으로 생성됩니다.
| ECMAScriptVersion | pickList | FileSelectionType == fileGlob && RuleLibrary == microsoft | True | ES3 | ES2015, ES2016, ES2017, ES3, ES5, ES6, ESNext | TypeScript 컴파일러를 사용하여 구성된 ECMAScript의 대상 버전입니다. 프로젝트 파일을 사용하는 경우 TypeScript tsconfig.json 파일의 compilerOptions.target 필드입니다.
| 프로젝트 | 문자열 | FileSelectionType == projectFile | True |  |  | TSLint를 실행할 TypeScript 파일을 지정하는 [tsconfig.json](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html) 파일에 대한 경로입니다. 경로는 `Build.SourcesDirectory` 값을 기준으로 상대적입니다.
| TypeCheck | boolean | RuleLibrary != microsoft && FileSelectionType == projectFile | False | true |  | linting 규칙을 실행할 때 형식 검사기를 사용하도록 설정합니다.
| ExcludeFiles | 문자열 | always | False |  |  | linting에서 제외할 파일을 나타내는 [glob](https://www.npmjs.com/package/glob)입니다. 경로는 `Build.SourcesDirectory` 값을 기준으로 상대적입니다. 여러 값을 세미콜론으로 구분하여 지정할 수 있습니다.
| OutputFormat | pickList | always | True | json : | checkstyle, codeFrame, filesList, json, msbuild, pmd, prose, stylish, verbose, vso | 출력을 생성하는 데 사용할 [포맷터](https://palantir.github.io/tslint/formatters/)입니다. JSON 형식은 사후 분석과 호환됩니다.
| NodeMemory | 문자열 | always | False |  |  | TSLint를 실행하기 위해 노드에 할당할 명시적 메모리 양(MB)입니다. 예: 8000<br/><br/>노드에 대한 `--max_old_space=<value>` CLI 옵션, 즉 `v8 option`에 매핑됩니다.
|  ToolVersion | pickList | RuleLibrary != microsoft | True | 최신 | 4.0.0, 4.0.1, 4.0.2, 4.1.0, 4.1.1, 4.2.0, 4.3.0, 4.3.1, 4.4.0, 4.4.1, 4.4.2, 4.5.0, 4.5.1, 5.0.0, 5.1.0, 5.2.0, 5.3.0, 5.3.2, 5.4.0, 5.4.1, 5.4.2, 5.4.3, 5.5.0, latest | 다운로드하고 실행할 TSLint의 [버전](https://github.com/palantir/tslint/releases)입니다.
| TypeScriptVersion | pickList | always | True | 최신 | 0.8.0, 0.8.1, 0.8.2, 0.8.3, 0.9.0, 0.9.1, 0.9.5, 0.9.7, 1.0.0, 1.0.1, 1.3.0, 1.4.1, 1.5.3, 1.6.2, 1.7.3, 1.7.5, 1.8.0, 1.8.10, 1.8.2, 1.8.5, 1.8.6, 1.8.7, 1.8.9, 1.9.0, 2.0.0, 2.0.10, 2.0.2, 2.0.3, 2.0.6, 2.0.7, 2.0.8, 2.0.9, 2.1.1, 2.1.4, 2.1.5, 2.1.6, 2.2.0, 2.2.1, custom, latest | 다운로드하고 사용할 [typescript](https://www.npmjs.com/package/typescript)의 버전입니다.<br/>**참고:** 코드를 컴파일하는 데 사용되는 것과 동일한 버전의 TypeScript여야 합니다.
| TypeScriptVersionCustom | 문자열 | TypeScriptVersion == custom | True | 최신 |  | 다운로드하고 사용할 [typescript](https://www.npmjs.com/package/typescript)의 버전입니다.<br/>**참고:** 코드를 컴파일하는 데 사용되는 것과 동일한 버전의 TypeScript여야 합니다.
| MicrosoftContribVersion | pickList | RuleLibrary == microsoft |  | 최신 | 4.0.0, 4.0.1, 5.0.0, 5.0.1, latest | 다운로드하고 사용할 [tslint-microsoft-contrib](https://www.npmjs.com/package/tslint-microsoft-contrib)(SDL 규칙)의 버전입니다.</br>**참고:** tslint-microsoft-contrib에 대해 선택한 버전과 호환되는 버전의 [tslint](https://www.npmjs.com/package/tslint)가 선택됩니다. tslint-microsoft-contrib에 대한 업데이트는 테스트 기간이 발생할 수 있을 때까지 이 빌드 작업에 의해 제어됩니다.

## <a name="publish-security-analysis-logs-task"></a>보안 분석 로그 게시 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ArtifactName | 문자열 | always | True | CodeAnalysisLogs |  | 만들 아티팩트의 이름입니다.
| ArtifactType | pickList | always | True | 컨테이너 | Container, FilePath | 만들 아티팩트의 형식입니다.
| TargetPath | 문자열 | ArtifactType = FilePath | False | \\my\share\$(Build.DefinitionName)<br>\$(Build.BuildNumber) |  | 파일을 복사할 파일 공유입니다.
| AllTools | boolean | always | True | true |  | 모든 Secure Development Tools 빌드 작업에서 생성된 결과를 게시합니다.
| AntiMalware | boolean | AllTools = false | True | true |  | AntiMalware 빌드 작업으로 생성된 결과를 게시합니다.
| BinSkim | boolean | AllTools = false | True | true |  | BinSkim 빌드 작업으로 생성된 결과를 게시합니다.
| CredScan | boolean | AllTools = false | True | true |  | Credential Scanner 빌드 작업으로 생성된 결과를 게시합니다.
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | Roslyn Analyzers 빌드 작업으로 생성된 결과를 게시합니다.
| TSLint | boolean | AllTools = false | True | true |  | TSLint 빌드 작업으로 생성된 결과를 게시합니다. JSON 형식의 TSLint 로그만 보고서에 대해 지원됩니다. 다른 형식을 선택한 경우에는 TSLint 빌드 작업을 적절하게 업데이트하세요.
| ToolLogsNotFoundAction | picklist | always | True | Standard | Error, None, Standard, Warning | 선택한 도구(AllTools를 선택한 경우 모든 도구)에 대한 로그를 찾을 수 없는 경우(도구가 실행되지 않음을 의미) 수행할 동작입니다.<br/><br/>**옵션:**<br/>**None:** 메시지가 VSTS 변수 **system.debug** 를 **true** 로 설정해야만 액세스할 수 있는 자세한 정보 출력 스트림에 기록됩니다.<br/>**Standard:** (기본값) 도구에 대한 로그를 찾을 수 없다는 표준 출력 메시지를 기록합니다.<br/>**Warning:** 도구에 대한 로그를 찾을 수 없다는 노란색 경고 메시지를 기록합니다. 이 메시지는 빌드 요약 페이지에 경고로 표시됩니다.<br/>**Error:** 빨간색 오류 메시지를 기록하고 빌드를 중단하는 예외를 throw합니다. 개별 도구 선택에서 어느 도구가 실행되었는지 확인하려면 이 옵션을 사용합니다.

## <a name="security-report-task"></a>보안 보고서 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VstsConsole | boolean | always | False | true |  | 결과를 파이프라인 콘솔에 기록합니다.
| TsvFile | boolean | always | False | true |  | 검색된 결과가 한 줄에 하나씩 표시되고 탭으로 결과에 대한 정보가 구분하는 tsv 파일(탭으로 구분된 값)을 생성합니다.
| HtmlFile | boolean | always | False | true |  | html 보고서 파일을 생성합니다.
| AllTools | boolean | always | True | false |  | 모든 Secure Development Tools 빌드 작업에서 생성된 결과를 보고합니다.
| BinSkim | boolean | AllTools = false | True | false |  | BinSkim 빌드 작업으로 생성된 결과를 보고합니다.
| BinSkimBreakOn | pickList | AllTools = true OR BinSkim = true | True | Error | Error, WarningAbove | 보고할 결과의 수준입니다.
| CredScan | boolean | AllTools = false | True | false |  | Credential Scanner 빌드 작업으로 생성된 결과를 보고합니다.
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | Roslyn Analyzer 빌드 작업으로 생성된 결과를 보고합니다.
| RoslynAnalyzersBreakOn | pickList | AllTools = true OR RoslynAnalyzers = true | True | Error | Error, WarningAbove | 보고할 결과의 수준입니다.
| TSLint | boolean | AllTools = false | True | false |  | TSLint 빌드 작업으로 생성된 결과를 보고합니다. JSON 형식의 TSLint 로그만 보고서에 대해 지원됩니다. 다른 형식을 선택한 경우에는 TSLint 빌드 작업을 적절하게 업데이트하세요.
| TSLintBreakOn | pickList | AllTools = true OR TSLint = true | True | Error | Error, WarningAbove | 보고할 결과의 수준입니다.
| ToolLogsNotFoundAction | picklist | always | True | Standard | Error, None, Standard, Warning | 선택한 도구(AllTools를 선택한 경우 모든 도구)에 대한 로그를 찾을 수 없는 경우(도구가 실행되지 않음을 의미) 수행할 동작입니다.<br/><br/>**옵션:**<br/>**None:** 메시지가 VSTS 변수 **system.debug** 를 **true** 로 설정해야만 액세스할 수 있는 자세한 정보 출력 스트림에 기록됩니다.<br/>**Standard:** (기본값) 도구에 대한 로그를 찾을 수 없다는 표준 출력 메시지를 기록합니다.<br/>**Warning:** 도구에 대한 로그를 찾을 수 없다는 노란색 경고 메시지를 기록합니다. 이 메시지는 빌드 요약 페이지에 경고로 표시됩니다.<br/>**Error:** 빨간색 오류 메시지를 기록하고 빌드를 중단하는 예외를 throw합니다. 개별 도구 선택에서 어느 도구가 실행되었는지 확인하려면 이 옵션을 사용합니다.
| CustomLogsFolder | 문자열 | always | False |  |  | 분석 도구 로그가 있는 기본 폴더입니다. 개별 로그 파일은 이 경로 아래에 각 도구의 이름을 딴 하위 폴더에 배치됩니다.

## <a name="post-analysis-task"></a>사후 분석 작업

| **InputType**      | **유형**     | **적용 가능**            | **필수** | **기본값**             | **옵션(picklist용)**                                   | **설명**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AllTools | boolean | always | True | false |  | Microsoft Security Code Analysis 빌드 작업에서 문제가 발견되면 빌드를 중단합니다.
| BinSkim | boolean | AllTools = false | True | false |  | 선택한 중단 옵션에 따라 BinSkim 문제가 발견되면 빌드를 중단합니다.
| BinSkimBreakOn | pickList | AllTools = true OR BinSkim = true | True | Error | Error, WarningAbove | 빌드를 중단할 문제의 수준입니다.
| CredScan | boolean | AllTools = false | True | false |  | Credential Scanner 문제가 발견되면 빌드를 중단합니다.
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | Roslyn Analyzers 문제가 발견되면 빌드를 중단합니다.
| RoslynAnalyzersBreakOn | pickList | AllTools = true OR RoslynAnalyzers = true | True | Error | Error, WarningAbove | 빌드를 중단할 문제의 수준입니다.
| TSLint | boolean | AllTools = false | True | false |  | TSLint 문제가 발견되면 빌드를 중단합니다. JSON 형식의 TSLint 로그만 사후 분석에 대해 지원됩니다. 다른 형식을 선택한 경우에는 TSLint 빌드 작업을 적절하게 업데이트하세요.
| TSLintBreakOn | pickList | AllTools = true OR TSLint = true | True | Error | Error, WarningAbove | 빌드를 중단할 문제의 수준입니다.
| VstsConsole | boolean | always | False | true |  | 결과를 파이프라인 콘솔에 기록합니다.
| ToolLogsNotFoundAction | picklist | always | True | Standard | Error, None, Standard, Warning | 선택한 도구(AllTools를 선택한 경우 모든 도구)에 대한 로그를 찾을 수 없는 경우(도구가 실행되지 않음을 의미) 수행할 동작입니다.<br/><br/>**옵션:**<br/>**None:** 메시지가 VSTS 변수 **system.debug** 를 **true** 로 설정해야만 액세스할 수 있는 자세한 정보 출력 스트림에 기록됩니다.<br/>**Standard:** (기본값) 도구에 대한 로그를 찾을 수 없다는 표준 출력 메시지를 기록합니다.<br/>**Warning:** 도구에 대한 로그를 찾을 수 없다는 노란색 경고 메시지를 기록합니다. 이 메시지는 빌드 요약 페이지에 경고로 표시됩니다.<br/>**Error:** 빨간색 오류 메시지를 기록하고 빌드를 중단하는 예외를 throw합니다. 개별 도구 선택에서 어느 도구가 실행되었는지 확인하려면 이 옵션을 사용합니다.

## <a name="next-steps"></a>다음 단계

보안 코드 분석 확장 및 제공된 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.yml)를 확인하세요.