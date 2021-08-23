---
title: Microsoft 보안 코드 분석 작업 사용자 지정
titleSuffix: Azure
description: 이 문서에서는 Microsoft 보안 코드 분석 확장에서 작업을 사용자 지정하는 방법을 설명합니다.
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
ms.openlocfilehash: 763369fbbd52ed14b547e2a23dfa0bb977a1b9a4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112116143"
---
# <a name="configure-and-customize-the-build-tasks"></a>빌드 작업 구성 및 사용자 지정

> [!Note]
> 2022년 3월 1일부로 MSCA(Microsoft 보안 코드 분석) 확장의 사용이 중지됩니다. 기존 MSCA 고객은 2022년 3월 1일까지 MSCA에 대한 액세스가 유지됩니다. Azure DevOps의 대체 옵션은 [OWASP 소스 코드 분석 도구](https://owasp.org/www-community/Source_Code_Analysis_Tools)를 참조하세요. GitHub로의 마이그레이션을 계획하는 고객의 경우 [Github 고급 보안](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)을 확인할 수 있습니다.

이 문서에서는 각 빌드 작업에서 사용할 수 있는 구성 옵션에 대해 자세히 설명합니다. 이 문서는 보안 코드 분석 도구에 대한 작업으로 시작하여 후처리 작업으로 끝납니다.

## <a name="anti-malware-scanner-task"></a>맬웨어 방지 스캐너 작업

>[!NOTE]
> 맬웨어 방지 스캐너 빌드 작업에는 Windows Defender를 사용하도록 설정된 빌드 에이전트가 필요합니다. 호스트된 Visual Studio 2017 이상에서는 이러한 에이전트를 제공합니다. 빌드 작업은 Visual Studio 2015 호스트된 에이전트에서 실행되지 않습니다.
>
> 해당 에이전트에서는 서명을 업데이트할 수 없지만 서명 후 경과된 시간이 항상 3시간 미만이어야 합니다.

작업 구성의 세부 정보는 다음 스크린샷 및 텍스트에 표시됩니다.

![맬웨어 방지 스캐너 빌드 작업 구성](./media/security-tools/5-add-anti-malware-task600.png)

스크린샷의 **유형** 목록 상자에서 **기본** 을 선택합니다. **사용자 지정** 을 선택하여 스캔을 사용자 지정하는 명령줄 인수를 제공합니다.

Windows Defender는 Windows 업데이트 클라이언트를 사용하여 서명을 다운로드하고 설치합니다. 빌드 에이전트에서 서명 업데이트가 실패할 경우 Windows 업데이트에서 **HRESULT** 오류 코드가 발생할 수 있습니다.

Windows 업데이트 오류 및 해당 문제의 해결 방법에 대한 자세한 내용은 [구성 요소별 Windows 업데이트 오류 코드](/windows/deployment/update/windows-update-error-reference) 및 TechNet 문서의 [Windows 업데이트 에이전트 - 오류 코드](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)를 참조하세요.

이 작업의 YAML 구성에 대한 자세한 내용은 [맬웨어 방지 YAML 옵션](yaml-configuration.md#anti-malware-scanner-task)을 확인하세요.

## <a name="binskim-task"></a>BinSkim 작업

> [!NOTE]
> BinSkim 작업을 실행하려면 빌드가 다음 조건 중 하나를 충족해야 합니다.
>  - 빌드가 관리 코드에서 이진 아티팩트를 생성합니다.
>  - BinSkim을 사용하여 분석하려는 이진 아티팩트가 커밋되어 있습니다.

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시됩니다.

![BinSkim 빌드 작업 구성](./media/security-tools/7-binskim-task-details.png)

- .pdb 디버그 파일이 생성되도록 빌드 구성을 디버그로 설정합니다. BinSkim은 해당 파일을 사용하여 출력 이진 파일의 문제를 소스 코드로 다시 매핑합니다.
- 자체 명령줄을 다시 검색하고 작성하지 않으려면 다음을 수행합니다.
     - **유형** 목록에서 **기본** 을 선택합니다.
     - **함수** 목록에서 **분석** 을 선택합니다.
- **대상** 에 파일, 디렉터리 또는 필터 패턴에 대한 지정자를 하나 이상 입력합니다. 해당 지정자는 분석할 하나 이상의 이진 파일로 확인됩니다.
    - 지정된 여러 대상을 세미콜론(;)으로 구분해야 합니다.
    - 지정자는 단일 파일이거나 와일드카드를 포함할 수 있습니다.
    - 디렉터리 사양은 항상 \\*로 끝나야 합니다.
    - 예:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- **유형** 목록에서 **명령줄** 을 선택하는 경우 binskim.exe를 실행해야 합니다.
     - binskim.exe의 첫 번째 인수가 동사 **analyze** 이며 뒤에 하나 이상의 경로 지정이 있는지 확인합니다. 각 경로는 전체 경로이거나 소스 디렉터리와 관련된 경로일 수 있습니다.
     - 여러 대상 경로는 공백으로 구분해야 합니다.
     - **/o** 또는 **/output** 옵션을 생략할 수 있습니다. 출력값이 추가되거나 대체되었습니다.
     - 표준 명령줄 구성은 다음과 같이 표시됩니다.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> 대상의 디렉터리를 지정하는 경우 후행 \\*가 중요합니다.

BinSkim 명령줄 인수, ID별 규칙 또는 종료 코드에 대한 자세한 내용은 [BinSkim 사용자 가이드](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)를 참조하세요.

이 작업의 YAML 구성에 대한 자세한 내용은 [BinSkim YAML 옵션](yaml-configuration.md#binskim-task)을 확인하세요.

## <a name="credential-scanner-task"></a>자격 증명 스캐너 작업

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시됩니다.

![자격 증명 스캐너 빌드 작업 구성](./media/security-tools/3-taskdetails.png)

사용 가능한 옵션은 다음과 같습니다.
  - **표시 이름**: Azure DevOps 작업의 이름입니다. 기본값은 자격 증명 스캐너 실행입니다.
  - **도구 주 버전**: 사용 가능한 값에는 **CredScan V2**, **CredScan V1** 이 포함됩니다. 당사는 고객에게 **CredScan V2** 버전 사용을 권장합니다.
  - **출력 형식**: 사용 가능한 값에는 **TSV**, **CSV**, **SARIF**, **PREfast** 가 포함됩니다.
  - **도구 버전**: **최신** 을 선택하는 것이 좋습니다.
  - **스캔 폴더**: 스캔할 리포지토리 폴더입니다.
  - **검색자 파일 형식**: 스캔에 사용되는 검색자 파일을 찾기 위한 옵션입니다.
  - **비표시 파일**: [JSON](https://json.org/) 파일은 출력 로그의 문제를 표시하지 않을 수 있습니다. 비표시 시나리오의 자세한 내용은 이 문서의 FAQ 섹션을 참조하세요.
  - **자세한 정보 출력**: 설명이 따로 필요하지 않습니다.
  - **일괄 처리 크기**: 자격 증명 스캐너를 실행하는 데 사용되는 동시 스레드의 수입니다. 기본값은 20입니다. 가능한 값의 범위는 1부터 2,147,483,647까지입니다.
  - **일치 제한 시간**: 검사를 중단하기 전에 검색자 일치를 시도하는 데 소요되는 시간(초)입니다.
  - **파일 검색 읽기 버퍼 크기**: 콘텐츠를 읽는 동안 사용된 버퍼의 크기(바이트)입니다. 기본값은 524,288입니다.  
  - **최대 파일 스캔 읽기 바이트**: 콘텐츠를 분석하는 동안 파일에서 읽을 최대 바이트 수입니다. 기본값은 104,857,600입니다.
  - **제어 옵션** > **이 작업 실행**: 작업을 실행할 시기를 지정합니다. 더 복잡한 조건을 지정하려면 **사용자 지정 조건** 을 선택합니다.
  - **버전**: Azure DevOps 내의 빌드 작업 버전입니다. 이 옵션은 자주 사용되지 않습니다.

이 작업의 YAML 구성에 대한 자세한 정보는 [자격 증명 스캐너 YAML 옵션](yaml-configuration.md#credential-scanner-task)을 확인하세요.

## <a name="roslyn-analyzers-task"></a>Roslyn 분석기 작업

> [!NOTE]
> Roslyn 분석기 작업을 실행하려면 빌드가 다음 조건을 충족해야 합니다.
>
> - 빌드 정의에 C# 또는 Visual Basic 코드를 컴파일하기 위한 기본 제공 MSBuild 또는 VSBuild가 포함되어 있습니다. 분석기 작업이 기본 제공 작업의 입력 및 출력을 사용하여 Roslyn 분석기를 사용하도록 설정된 MSBuild 컴파일을 실행합니다.
> - 이 빌드 작업을 실행하는 빌드 에이전트에 Visual Studio 2017 버전 15.5 이상이 설치되어 있어 컴파일러 버전 2.6 이상의 사용을 지원합니다.

작업 구성의 세부 정보는 다음 목록 및 메모에 표시됩니다.

사용 가능한 옵션은 다음과 같습니다.

- **규칙 집합**: 값에는 **SDL 필수**, **SDL 권장** 또는 자체 사용자 지정 규칙 집합이 있습니다.
- **분석기 버전**: **최신** 을 선택하는 것이 좋습니다.
- **컴파일러 경고 비표시 파일**: 표시되지 않는 경고 ID 목록이 포함된 텍스트 파일입니다.
- **제어 옵션** > **이 작업 실행**: 작업을 실행할 시기를 지정합니다. 더 복잡한 조건을 지정하려면 **사용자 지정 조건** 을 선택합니다.

> [!NOTE]
>
> - Roslyn 분석기는 컴파일러와 통합되며 csc.exe 컴파일의 일부로만 실행할 수 있습니다. 따라서 이 작업을 수행하려면 빌드에서 이전에 실행된 컴파일러 명령을 재생하거나 다시 실행해야 합니다. 이 재생 또는 실행은 MSBuild 빌드 작업 로그에 대해 Azure DevOps(이전의 Visual Studio Team Services)를 쿼리하여 수행됩니다.
>
>   작업이 빌드 정의에서 MSBuild 컴파일 명령줄을 안정적으로 가져올 수 있는 다른 방법은 없습니다. 사용자가 명령줄을 입력할 수 있도록 자유형 텍스트 상자를 추가하는 것도 고려했지만 그렇게 하면 명령줄을 최신 상태로 유지하고 기본 빌드와 동기화하는 것이 어려워집니다.
>
>   사용자 지정 빌드를 수행하려면 컴파일러 명령뿐 아니라 전체 명령 집합을 재생해야 합니다. 이 경우 Roslyn 분석기를 활성화하는 것은 간단하거나 안정적이지 않습니다.
>
> - Roslyn 분석기는 컴파일러와 통합됩니다. 호출하려면 Roslyn 분석기에 컴파일이 필요합니다.
>
>   해당하는 새 빌드 작업은 이미 빌드된 C# 프로젝트를 다시 컴파일하여 구현됩니다. 새 작업은 원래 작업과 동일한 빌드 또는 빌드 정의에서 MSBuild 및 VSBuild 빌드 작업만 사용합니다. 그러나 이 경우 새 작업은 Roslyn 분석기를 사용하도록 설정한 상태에서 이를 사용합니다.
>
>   새 작업이 원래 작업과 동일한 에이전트에서 실행되는 경우 새 작업의 출력은 *s* 소스 폴더에 있는 원래 작업의 출력을 덮어씁니다. 빌드 출력이 동일하더라도 MSBuild를 실행하고 출력을 아티팩트 스테이징 디렉터리로 복사한 다음, Roslyn 분석기를 실행하는 것이 좋습니다.

Roslyn 분석기 작업에 대한 추가 리소스는 Microsoft Docs의 [Roslyn 기반 분석기](/dotnet/standard/analyzers/api-analyzer)를 확인하세요.

이 빌드 작업에서 설치하고 사용하는 분석기 패키지는 NuGet 페이지 [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)에서 찾을 수 있습니다.

이 작업의 YAML 구성에 대한 자세한 내용은 [Roslyn 분석기 YAML 옵션](yaml-configuration.md#roslyn-analyzers-task)을 확인하세요.

## <a name="tslint-task"></a>TSLint 작업

TSLint에 대한 자세한 내용은 [TSLint GitHub 리포지토리](https://github.com/palantir/tslint)로 이동하세요.

>[!NOTE] 
>이미 알고 계실 수도 있지만 [TSLint GitHub 리포지토리](https://github.com/palantir/tslint) 홈페이지에 의하면 TSLint는 2019년의 특정 시점부터 사용되지 않을 예정입니다. Microsoft는 이에 대한 대체 작업으로 [ESLint](https://github.com/eslint/eslint)를 조사하고 있습니다.

이 작업의 YAML 구성에 대한 자세한 내용은 [TSLint YAML 옵션](yaml-configuration.md#tslint-task)을 확인하세요.

## <a name="publish-security-analysis-logs-task"></a>보안 분석 로그 게시 작업

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시됩니다.

![보안 분석 로그 빌드 게시 작업 구성](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **아티팩트 이름**: 임의의 문자열 식별자입니다.
- **아티팩트 형식**: 선택에 따라 로그를 Azure DevOps Server 또는 빌드 에이전트에 액세스할 수 있는 공유 파일에 게시할 수 있습니다.
- **도구**: 특정 도구에 대한 로그를 보존하도록 선택하거나 **모든 도구** 를 선택하여 모든 로그를 보존할 수 있습니다.

이 작업의 YAML 구성에 대한 자세한 내용은 [보안 로그 게시 YAML 옵션](yaml-configuration.md#publish-security-analysis-logs-task)을 확인하세요.

## <a name="security-report-task"></a>보안 보고서 작업

보안 보고서 구성의 세부 정보는 다음 스크린샷 및 목록에 표시됩니다.

![보안 보고서 빌드 작업 구성](./media/security-tools/4-createsecurityanalysisreport600.png)

- **보고서**: **파이프라인 콘솔**, **TSV 파일**, **Html 파일** 형식 중 하나를 선택합니다. 선택한 각 형식에 대해 하나의 보고서 파일이 생성됩니다.
- **도구**: 검색된 문제의 요약을 원하는 빌드 정의의 도구를 선택합니다. 선택한 각 도구에 대해 요약 보고서에 오류만 표시할지 혹은 오류와 경고를 모두 표시할지 선택하는 옵션이 있을 수 있습니다.
- **고급 옵션**: 선택한 도구 중 하나에 대해 로그가 없는 경우 경고 또는 오류를 로그하도록 선택할 수 있습니다. 오류를 로그하면 작업이 실패합니다.
- **기본 로그 폴더**: 로그를 찾을 수 있는 기본 로그 폴더를 사용자 지정할 수 있습니다. 그러나 이 옵션은 일반적으로 사용되지 않습니다.

이 작업의 YAML 구성에 대한 자세한 내용은 [보안 보고서 YAML 옵션](yaml-configuration.md#security-report-task)을 확인하세요.

## <a name="post-analysis-task"></a>사후 분석 작업

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시됩니다.

![사후 분석 빌드 작업 구성](./media/security-tools/a-post-analysis600.png)

- **도구**: 조건부로 빌드 중단을 삽입하려는 빌드 정의에서 도구를 선택합니다. 선택한 각 도구에 대해 오류 시에만 중단할지 혹은 오류와 경고 시에 모두 중단할지 선택하는 옵션이 있을 수 있습니다.
- **보고서**: 필요에 따라 빌드 중단을 발생시키는 결과에 대해 작성할 수 있습니다. 결과는 Azure DevOps 콘솔 창 및 로그 파일에 작성됩니다.
- **고급 옵션**: 선택한 도구 중 하나에 대해 로그가 없는 경우 경고 또는 오류를 로그하도록 선택할 수 있습니다. 오류를 로그하면 작업이 실패합니다.

이 작업의 YAML 구성에 대한 자세한 내용은 [사후 분석 YAML 옵션](yaml-configuration.md#post-analysis-task)을 확인하세요.

## <a name="next-steps"></a>다음 단계

YAML 기반 구성에 대한 자세한 내용은 [YAML 구성 가이드](yaml-configuration.md)를 참조하세요.

보안 코드 분석 확장 및 제공된 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.yml)를 확인하세요.