---
title: Microsoft 보안 코드 분석 작업 사용자 지정
titleSuffix: Azure
description: 이 문서에서는 Microsoft 보안 코드 분석 확장에서 작업을 사용자 지정 하는 방법을 설명 합니다.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 744b186b32927f81be21ff067c9195bddb33c416
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362094"
---
# <a name="configure-and-customize-the-build-tasks"></a>빌드 작업 구성 및 사용자 지정

이 문서에서는 각 빌드 작업에서 사용할 수 있는 구성 옵션에 대해 자세히 설명 합니다. 이 문서는 보안 코드 분석 도구에 대 한 작업으로 시작 합니다. 사후 처리 작업으로 끝납니다.

## <a name="anti-malware-scanner-task"></a>맬웨어 방지 스캐너 작업

>[!NOTE]
> 맬웨어 방지 스캐너 빌드 작업에는 Windows Defender를 사용 하는 빌드 에이전트가 필요 합니다. 호스팅된 Visual Studio 2017 이상에서는 이러한 에이전트를 제공 합니다. 빌드 작업은 Visual Studio 2015 호스팅된 에이전트에서 실행 되지 않습니다.
>
> 이러한 에이전트에서는 시그니처를 업데이트할 수 없지만 서명이 항상 3 시간 미만 이어야 합니다.

작업 구성의 세부 정보는 다음 스크린샷 및 텍스트에 표시 됩니다.

![맬웨어 방지 스캐너 빌드 작업 구성](./media/security-tools/5-add-anti-malware-task600.png)

스크린샷의 **유형** 목록 상자에서 **기본** 을 선택 합니다. **사용자 지정** 을 선택 하 여 검색을 사용자 지정 하는 명령줄 인수를 제공 합니다.

Windows Defender는 Windows 업데이트 클라이언트를 사용 하 여 서명을 다운로드 하 고 설치 합니다. 빌드 에이전트에서 서명 업데이트가 실패할 경우 **HRESULT** 오류 코드는 Windows 업데이트에서 발생할 수 있습니다.

Windows 업데이트 오류 및 해당 문제 해결에 대 한 자세한 내용은 [Windows 업데이트 오류 코드를 구성 요소](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) 및 TechNet 문서 [Windows 업데이트 에이전트-오류 코드](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)를 참조 하세요.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [맬웨어 방지 yaml 옵션](yaml-configuration.md#anti-malware-scanner-task) 을 확인 하세요.

## <a name="binskim-task"></a>BinSkim 작업

> [!NOTE]
> BinSkim 작업을 실행 하기 전에 빌드는 다음 조건 중 하나를 충족 해야 합니다.
>  - 빌드는 관리 코드에서 이진 아티팩트를 생성 합니다.
>  - BinSkim를 사용 하 여 분석 하려는 이진 아티팩트가 커밋 되었습니다.

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시 됩니다.

![BinSkim 빌드 작업 구성](./media/security-tools/7-binskim-task-details.png)

- .Pdb 디버그 파일이 생성 되도록 빌드 구성을 디버그로 설정 합니다. BinSkim은 이러한 파일을 사용 하 여 출력 이진 파일의 문제를 소스 코드로 다시 매핑합니다.
- 직접 작성 한 명령줄을 사용 하지 않으려면 다음을 수행 합니다.
     - **유형** 목록에서 **기본**을 선택 합니다.
     - **함수** 목록에서 **분석**을 선택 합니다.
- **대상**에 파일, 디렉터리 또는 필터 패턴에 대 한 지정자를 하나 이상 입력 합니다. 이러한 지정자는 분석할 이진 파일 하나 이상으로 확인 됩니다.
    - 지정 된 여러 대상을 세미콜론 (;)으로 구분 해야 합니다.
    - 지정자는 단일 파일 이거나 와일드 카드를 포함할 수 있습니다.
    - 디렉터리 사양은 항상 *로 끝나야 합니다 \\ .
    - 예:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- **유형** 목록에서 **명령줄** 을 선택 하는 경우 binskim.exe를 실행 해야 합니다.
     - binskim.exe에 대 한 첫 번째 인수가 동사 **분석** 뒤에 하나 이상의 경로 지정이 있는지 확인 합니다. 각 경로는 전체 경로 이거나 원본 디렉터리를 기준으로 하는 경로일 수 있습니다.
     - 여러 대상 경로는 공백으로 구분 해야 합니다.
     - **/O** 또는 **/output** 옵션은 생략할 수 있습니다. 출력 값이 추가 되거나 대체 됩니다.
     - 표준 명령줄 구성은 다음과 같이 표시 됩니다.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > \\대상에 대 한 디렉터리를 지정 하는 경우에는 후행 *이 중요 합니다.

BinSkim 명령줄 인수, ID 별 규칙 또는 종료 코드에 대 한 자세한 내용은 [BinSkim 사용자 가이드](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)를 참조 하세요.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [BINSKIM yaml 옵션](yaml-configuration.md#binskim-task) 을 확인 하세요.

## <a name="credential-scanner-task"></a>자격 증명 스캐너 작업

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시 됩니다.

![자격 증명 스캐너 빌드 작업 구성](./media/security-tools/3-taskdetails.png)

사용 가능한 옵션은 다음과 같습니다.
  - **표시 이름**: Azure Devops 태스크의 이름입니다. 기본값은 자격 증명 검색 프로그램을 실행 합니다.
  - **도구 주 버전**: 사용 가능한 값에는 **credscan V2**, **credscan V1**이 포함 됩니다. 고객은 **Credscan V2** 버전을 사용 하는 것이 좋습니다.
  - **출력 형식**: 사용 가능한 값은 **TSV**, **CSV**, **SARIF**및 **PREfast**입니다.
  - **도구 버전**: **최신**을 선택 하는 것이 좋습니다.
  - **검색 폴더**: 검색할 리포지토리 폴더입니다.
  - **Searchers 파일 형식**: 검색에 사용 되는 Searchers 파일을 찾기 위한 옵션입니다.
  - **비 표시 파일**: [JSON](https://json.org/) 파일은 출력 로그의 문제를 억제할 수 있습니다. 억제 시나리오에 대 한 자세한 내용은이 문서의 FAQ 섹션을 참조 하세요.
  - **자세한 정보 출력**: 설명이 필요 없습니다.
  - **일괄 처리 크기**: 자격 증명 검색 프로그램을 실행 하는 데 사용 되는 동시 스레드 수입니다. 기본값은 20입니다. 가능한 값의 범위는 1 ~ 2147483647입니다.
  - **일치 제한 시간**: 검사를 포기 하기 전에 검색 자가 일치를 시도 하는 데 걸리는 시간 (초)입니다.
  - **파일 검색 읽기 버퍼 크기**: 콘텐츠를 읽는 동안 사용 된 버퍼의 크기 (바이트)입니다. 기본값은 524288입니다.  
  - **최대 파일 검색 읽기 바이트**: 콘텐츠를 분석 하는 동안 파일에서 읽을 최대 바이트 수입니다. 기본값은 104857600입니다.
  - **제어 옵션**  >  **이 작업 실행**: 작업을 실행할 시기를 지정 합니다. 더 복잡 한 조건을 지정 하려면 **사용자 지정 조건** 을 선택 합니다.
  - **버전**: Azure devops 내의 빌드 작업 버전입니다. 이 옵션은 자주 사용 되지 않습니다.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [자격 증명 스캐너 yaml 옵션](yaml-configuration.md#credential-scanner-task) 을 확인 하세요.

## <a name="roslyn-analyzers-task"></a>Roslyn 분석기 작업

> [!NOTE]
> Roslyn 분석기 작업을 실행 하기 전에 빌드는 다음 조건을 충족 해야 합니다.
>
> - 빌드 정의에는 c # 또는 Visual Basic 코드를 컴파일할 수 있는 기본 제공 MSBuild 또는 VSBuild 빌드 작업이 포함 되어 있습니다. 분석기 작업은 기본 제공 작업의 입력 및 출력을 사용 하 여 Roslyn 분석기를 사용 하는 MSBuild 컴파일을 실행 합니다.
> - 이 빌드 작업을 실행 하는 빌드 에이전트에는 Visual Studio 2017 버전이 15.5 이상 설치 되어 있으므로 컴파일러 버전 2.6 이상을 사용 합니다.

작업 구성의 세부 정보는 다음 목록과 메모에 나와 있습니다.

사용 가능한 옵션은 다음과 같습니다.

- **규칙 집합**: 값은 **sdl 필수**, **sdl 권장**또는 고유한 사용자 지정 규칙 집합입니다.
- **분석기 버전**: **최신**버전을 선택 하는 것이 좋습니다.
- **컴파일러 경고**표시 안 함 파일: 표시 되지 않는 경고 id 목록이 포함 된 텍스트 파일입니다.
- **제어 옵션**  >  **이 작업 실행**: 작업을 실행할 시기를 지정 합니다. 더 복잡 한 조건을 지정 하려면 **사용자 지정 조건** 을 선택 합니다.

> [!NOTE]
>
> - Roslyn 분석기는 컴파일러와 통합 되며 csc.exe 컴파일의 일부로만 실행할 수 있습니다. 따라서이 작업을 수행 하려면 빌드에서 이전에 실행 된 컴파일러 명령을 재생 하거나 다시 실행 해야 합니다. 이 재생 또는 실행은 MSBuild 빌드 작업 로그에 대해 VSTS (Visual Studio Team Services)를 쿼리하여 수행 됩니다.
>
>   태스크가 빌드 정의에서 MSBuild 컴파일 명령줄을 안정적으로 가져올 수 있는 다른 방법은 없습니다. 사용자가 명령줄을 입력할 수 있도록 자유형 텍스트 상자를 추가 하는 것으로 간주 됩니다. 그러나 이러한 명령줄을 최신 상태로 유지 하 고 주 빌드와 동기화 하기는 어렵습니다.
>
>   사용자 지정 빌드를 수행 하려면 컴파일러 명령 뿐만 아니라 전체 명령 집합을 재생 해야 합니다. 이러한 경우 Roslyn 분석기를 사용 하는 것은 간단 하거나 안정적이 아닙니다.
>
> - Roslyn 분석기는 컴파일러와 통합 됩니다. Roslyn 분석기를 호출 하려면 컴파일이 필요 합니다.
>
>   이 새 빌드 작업은 이미 빌드된 c # 프로젝트를 다시 컴파일하여 구현 됩니다. 새 작업은 원래 작업과 동일한 빌드 또는 빌드 정의에서 MSBuild 및 VSBuild 빌드 작업만 사용 합니다. 그러나이 경우 새 작업은 Roslyn 분석기를 사용 하도록 설정한 상태에서이를 사용 합니다.
>
>   새 태스크가 원래 작업과 동일한 에이전트에서 실행 되는 경우 새 작업의 출력은 *s* sources 폴더에 있는 원래 작업의 출력을 덮어씁니다. 빌드 출력이 동일 하더라도 MSBuild를 실행 하 고 출력을 아티팩트 스테이징 디렉터리에 복사한 다음 Roslyn 분석기를 실행 하는 것이 좋습니다.

Roslyn 분석기 태스크에 대 한 추가 리소스는 Microsoft Docs의 [Roslyn 기반 분석기](https://docs.microsoft.com/dotnet/standard/analyzers/) 를 확인 하세요.

이 빌드 작업에서 설치 하 고 사용 하는 분석기 패키지는 NuGet 페이지 [FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)에서 찾을 수 있습니다.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [Roslyn 분석기 yaml 옵션](yaml-configuration.md#roslyn-analyzers-task) 을 확인 하세요.

## <a name="tslint-task"></a>TSLint 작업

TSLint에 대 한 자세한 내용은 [TSLint GitHub 리포지토리](https://github.com/palantir/tslint)를 참조 하세요.

>[!NOTE] 
>[TSLint GitHub 리포지토리](https://github.com/palantir/tslint) 홈 페이지에서는 TSLint가 2019에 사용 되지 않을 예정입니다. Microsoft는 [Eslint](https://github.com/eslint/eslint) 대체 작업으로 조사 하 고 있습니다.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [TSLINT yaml 옵션](yaml-configuration.md#tslint-task) 을 확인 하세요.

## <a name="publish-security-analysis-logs-task"></a>보안 분석 로그 게시 태스크

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시 됩니다.

![보안 분석 로그 게시 빌드 작업 구성](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **아티팩트 이름**: 임의의 문자열 식별자입니다.
- **아티팩트 형식**: 선택 항목에 따라 로그를 Azure DevOps Server 또는 빌드 에이전트에서 액세스할 수 있는 공유 파일에 게시할 수 있습니다.
- **도구**: 특정 도구에 대 한 로그를 보존 하도록 선택 하거나 모든 **도구** 를 선택 하 여 모든 로그를 보존할 수 있습니다.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [게시 보안 로그 yaml 옵션](yaml-configuration.md#publish-security-analysis-logs-task) 을 확인 하세요.

## <a name="security-report-task"></a>보안 보고서 작업

보안 보고서 구성의 세부 정보는 다음 스크린샷 및 목록에 표시 됩니다.

![보안 보고서 빌드 작업 구성](./media/security-tools/4-createsecurityanalysisreport600.png)

- **보고서**: **파이프라인 콘솔**, **TSV 파일**및 **Html 파일** 형식을 선택 합니다. 선택한 각 형식에 대해 하나의 보고서 파일이 생성 됩니다.
- **도구**: 검색 된 문제의 요약을 원하는 빌드 정의의 도구를 선택 합니다. 선택한 각 도구에 대해 오류만 표시 하는지 아니면 요약 보고서에 오류 및 경고를 모두 표시할지를 선택 하는 옵션이 있을 수 있습니다.
- **고급 옵션**: 선택한 도구 중 하나에 대 한 로그가 없는 경우 경고 또는 오류를 기록 하도록 선택할 수 있습니다. 오류를 기록 하면 태스크가 실패 합니다.
- **기본 로그 폴더**: 로그가 있는 기본 로그 폴더를 사용자 지정할 수 있습니다. 그러나이 옵션은 일반적으로 사용 되지 않습니다.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [보안 보고서 yaml 옵션](yaml-configuration.md#security-report-task) 을 확인 하세요.

## <a name="post-analysis-task"></a>사후 분석 작업

작업 구성의 세부 정보는 다음 스크린샷 및 목록에 표시 됩니다.

![사후 분석 빌드 작업 구성](./media/security-tools/a-post-analysis600.png)

- **도구**: 빌드 정의에서 조건적으로 빌드 중단을 삽입 하려는 도구를 선택 합니다. 선택한 각 도구에 대해 오류를 중단할지 아니면 오류와 경고 모두를 중단할지를 선택할 수 있는 옵션이 있습니다.
- **보고서**: 필요에 따라 빌드 중단을 발생 시키는 결과를 쓸 수 있습니다. 결과는 Azure DevOps 콘솔 창 및 로그 파일에 기록 됩니다.
- **고급 옵션**: 선택한 도구 중 하나에 대 한 로그가 없는 경우 경고 또는 오류를 기록 하도록 선택할 수 있습니다. 오류를 기록 하면 태스크가 실패 합니다.

이 작업에 대 한 YAML 구성에 대 한 자세한 내용은 [사후 분석 yaml 옵션](yaml-configuration.md#post-analysis-task) 을 확인 하세요.

## <a name="next-steps"></a>다음 단계

YAML 기반 구성에 대 한 자세한 내용은 [Yaml 구성 가이드](yaml-configuration.md)를 참조 하세요.

보안 코드 분석 확장 및 제공 된 도구에 대 한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인 하세요.