---
title: Microsoft 보안 코드 분석 작업 사용자 지정
titleSuffix: Azure
description: 이 문서에서는 Microsoft 보안 코드 분석 확장의 작업 사용자 지정에 대해 설명합니다.
author: vharindra
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499994"
---
# <a name="configure-and-customize-the-build-tasks"></a>빌드 작업 구성 및 사용자 지정

이 문서에서는 각 빌드 작업에서 사용할 수 있는 구성 옵션에 대해 자세히 설명합니다. 이 문서는 보안 코드 분석 도구에 대한 작업으로 시작합니다. 사후 처리 작업으로 끝납니다.

## <a name="anti-malware-scanner-task"></a>맬웨어 방지 스캐너 작업

>[!NOTE]
> 맬웨어 방지 스캐너 빌드 작업에는 Windows Defender가 활성화된 빌드 에이전트가 필요합니다. 호스팅 Visual Studio 2017 및 나중에 이러한 에이전트를 제공합니다. 빌드 작업은 Visual Studio 2015 호스팅 에이전트에서 실행되지 않습니다.
>
> 이러한 에이전트에서 서명을 업데이트할 수는 없지만 서명은 항상 3시간 미만이어야 합니다.

작업 구성에 대한 자세한 내용은 다음 스크린샷 과 텍스트에 표시됩니다.

![맬웨어 방지 스캐너 빌드 작업 구성](./media/security-tools/5-add-anti-malware-task600.png)

스크린샷의 **유형** 목록 상자에서 **기본이** 선택됩니다. 검색을 사용자 지정하는 명령줄 인수를 제공하려면 **Custom을** 선택합니다.

Windows Defender는 Windows 업데이트 클라이언트를 사용하여 서명을 다운로드하고 설치합니다. 빌드 에이전트에서 서명 업데이트에 실패하면 Windows 업데이트에서 **HRESULT** 오류 코드가 생성될 수 있습니다.

Windows 업데이트 오류 및 해당 완화에 대한 자세한 내용은 [구성 요소별 Windows 업데이트 오류 코드](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) 및 TechNet 문서 Windows 업데이트 에이전트 - 오류 [코드를](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)참조하십시오.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [맬웨어 방지 YAML 옵션을](yaml-configuration.md#anti-malware-scanner-task) 확인하십시오.

## <a name="binskim-task"></a>빈스킴 작업

> [!NOTE]
> BinSkim 작업을 실행하려면 먼저 빌드가 다음 조건 중 하나를 충족해야 합니다.
>  - 빌드는 관리 코드에서 이진 아티팩트를 생성합니다.
>  - BinSkim으로 분석하려는 이진 아티팩트가 있습니다.

작업 구성에 대한 자세한 내용은 다음 스크린샷 및 목록에 표시됩니다.

![BinSkim 빌드 작업 구성](./media/security-tools/7-binskim-task-details.png)

- .pdb 디버그 파일이 생성되도록 빌드 구성을 디버그로 설정합니다. BinSkim은 이러한 파일을 사용하여 출력 바이너리의 문제를 소스 코드로 다시 매핑합니다.
- 사용자 고유의 명령줄을 조사하고 만들지 않으려면 다음을 수행하십시오.
     - **유형** 목록에서 **기본**을 선택합니다.
     - **함수** 목록에서 **[분석]을**선택합니다.
- **Target에서**파일, 디렉터리 또는 필터 패턴에 대해 하나 이상의 지정자를 입력합니다. 이러한 지정자는 분석할 하나 이상의 바이너리로 해결됩니다.
    - 지정된 여러 대상은 세미콜론(;) 구분해야 합니다.
    - 지정자는 단일 파일이거나 와일드카드를 포함할 수 있습니다.
    - 디렉터리 사양은 항상 \\*로 끝나야 합니다.
    - 예제:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- **유형** 목록에서 **명령줄을** 선택한 경우 binskim.exe를 실행해야 합니다.
     - binskim.exe에 대한 첫 번째 인수가 동사 **분석뒤에** 하나 이상의 경로 사양이 있는지 확인합니다. 각 경로는 전체 경로이거나 원본 디렉터리와 관련된 경로일 수 있습니다.
     - 여러 대상 경로는 공백으로 구분되어야 합니다.
     - **/o** 또는 **/출력** 옵션을 생략할 수 있습니다. 출력 값이 추가되었거나 대체됩니다.
     - 표준 명령줄 구성은 다음과 같이 표시됩니다.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > 대상에 \\대한 디렉터리를 지정하는 경우 후행 * 중요합니다.

BinSkim 명령줄 인수, ID별 규칙 또는 종료 코드에 대한 자세한 내용은 [BinSkim 사용자 가이드를](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)참조하십시오.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [BinSkim YAML 옵션을](yaml-configuration.md#binskim-task) 확인하십시오.

## <a name="credential-scanner-task"></a>자격 증명 스캐너 작업

작업 구성에 대한 자세한 내용은 다음 스크린샷 및 목록에 표시됩니다.

![자격 증명 스캐너 빌드 작업 구성](./media/security-tools/3-taskdetails.png)

사용 가능한 옵션은 다음과 같습니다.

  - **출력 형식**: 사용 가능한 값에는 **TSV,** **CSV,** **SARIF**및 **PREfast가**포함됩니다.
  - **도구 버전**: **최신**을 선택하는 것이 좋습니다.
  - **폴더 스캔**: 검색할 리포지토리 폴더입니다.
  - **검색자 파일 유형**: 검색에 사용되는 검색기 파일을 찾는 옵션입니다.
  - **억제 파일**: [JSON](https://json.org/) 파일은 출력 로그의 문제를 억제할 수 있습니다. 억제 시나리오에 대한 자세한 내용은 이 문서의 FAQ 섹션을 참조하십시오.
  - **자세한 출력**: 자명한 설명.
  - **일괄 처리 크기**: 자격 증명 스캐너를 실행하는 데 사용되는 동시 스레드 수입니다. 기본값은 20입니다. 가능한 값범위는 1에서 2,147,483,647까지입니다.
  - **일치 시간 초과:** 수표를 포기하기 전에 검색자 일치를 시도하는 데 소요되는 시간(초)입니다.
  - **파일 검색 읽기 버퍼 크기**: 콘텐츠를 읽는 동안 사용되는 버퍼의 바이트 크기입니다. 기본값은 524,288입니다.  
  - **최대 파일 검색 바이트:** 콘텐츠 분석 중에 파일에서 읽을 수 있는 최대 바이트 수입니다. 기본값은 104,857,600입니다.
  - **제어 옵션** > **이 작업 실행**: 작업이 실행되는 시기를 지정합니다. **사용자 지정 조건을** 선택하여 보다 복잡한 조건을 지정합니다.
  - **버전**: Azure DevOps 내의 빌드 작업 버전입니다. 이 옵션은 자주 사용되지 않습니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [자격 증명 스캐너 YAML 옵션을](yaml-configuration.md#credential-scanner-task) 확인하십시오.

## <a name="microsoft-security-risk-detection-task"></a>Microsoft 보안 위험 검색 작업

> [!NOTE]
> MSRD 작업을 사용하기 전에 MSRD(보안 위험 감지) 서비스를 사용하여 계정을 만들고 구성해야 합니다. 이 서비스에는 별도의 온보딩 프로세스가 필요합니다. 이 확장의 대부분의 다른 작업과 달리 이 작업에는 MSRD를 가진 별도의 구독이 필요합니다.
>
> [Microsoft 보안 위험 검색](https://aka.ms/msrddocs) 및 Microsoft 보안 위험 [검색:](https://docs.microsoft.com/security-risk-detection/how-to/) 지침을 보려면 방법을 참조하십시오.

이 작업을 구성하는 세부 정보는 다음 목록에 나와 있습니다. 모든 UI 요소의 경우 해당 요소 위로 마우스를 가져가 도움을 받을 수 있습니다.

   - **MSRD에 대한 Azure DevOps 서비스 끝점 이름**: Azure DevOps 서비스 엔드포인트의 일반 유형은 온보데된 MSRD 인스턴스 URL과 REST API 액세스 토큰을 저장합니다. 이러한 끝점을 만든 경우 여기에서 지정할 수 있습니다. 그렇지 않으면 **관리** 링크를 선택하여 이 MSRD 작업에 대한 새 서비스 끝점을 만들고 구성합니다.
   - **계정 ID**: MSRD 계정 URL에서 검색할 수 있는 GUID입니다.
   - **바이너리URL**: 공개적으로 사용 가능한 URL의 세미콜론으로 구분된 목록입니다. 퍼징 머신은 이러한 URL을 사용하여 바이너리를 다운로드합니다.
   - **시드 파일의 URL**: 공개적으로 사용할 수 있는 URL의 세미콜론 으로 구분된 목록입니다. 퍼징 머신은 이러한 URL을 사용하여 시드를 다운로드합니다. 시드 파일이 바이너리와 함께 다운로드되는 경우 이 값을 지정하는 것은 선택 사항입니다.
   - **OS 플랫폼 유형**: 퍼징 작업을 실행하는 컴퓨터의 운영 체제(OS) 플랫폼입니다. 사용 가능한 값은 **윈도우와** **리눅스입니다.**
   - **윈도우 에디션 / 리눅스 에디션**: 퍼징 작업을 실행하는 컴퓨터의 OS 버전. 컴퓨터에 다른 OS 버전이 있는 경우 기본값을 덮어쓸 수 있습니다.
   - **패키지 설치 스크립트**: 테스트 컴퓨터에서 실행할 스크립트입니다. 이 스크립트는 퍼징 작업이 제출되기 전에 테스트 대상 프로그램과 해당 종속성을 설치합니다.
   - **작업 제출 매개 변수**:
       - **시드 디렉터리**: 시드를 포함하는 퍼징 컴퓨터의 디렉터리경로입니다.
       - **시드 확장자**: 시드의 파일 이름 확장명명입니다.
       - **테스트 드라이버 실행 파일**: 퍼징 컴퓨터에서 대상 실행 파일로 가는 경로입니다.
       - **테스트 드라이버 실행 파일 아키텍처**: 대상 실행 파일의 아키텍처입니다. 사용 가능한 값은 **x86** 및 **amd64입니다.**
       - **테스트 드라이버 인수**: 명령줄 인수가 테스트 실행 파일 파일에 전달되었습니다. 따옴표를 포함한 인수 "%testfile%"는 대상 파일에 대한 전체 경로로 자동으로 대체됩니다. 이 파일은 테스트 드라이버에 의해 구문 분석되며 필수입니다.
       - **테스트 드라이버 프로세스 종료 시**완료 : 테스트 드라이버가 완료되면 종료될 경우 이 확인란을 선택합니다. 테스트 드라이버를 강제로 닫아야 하는 경우 이 드라이버를 지웁힙으로 지웁힙으로 지웁힙으로 지웁힙합니다.
       - **최대 지속 시간(초)**: 대상 프로그램이 입력 파일을 구문 분석하는 데 필요한 합리적으로 예상되는 가장 긴 시간을 추정합니다. 예측이 정확할수록 퍼징 앱이 더 효율적으로 실행됩니다.
       - **테스트 드라이버를 반복적으로 실행할 수 있습니다**: 지속적 또는 공유 전역 상태에 따라 테스트 드라이버가 반복적으로 실행될 수 있는 경우 이 확인란을 선택합니다.
       - **테스트 드라이버의 이름을 바꿀 수 있습니다**: 테스트 드라이버 실행 파일의 이름을 바꿀 수 있고 여전히 올바르게 작동하는 경우 이 확인란을 선택합니다.
       - **퍼징 응용 프로그램은 단일 OS 프로세스로 실행**: 테스트 드라이버가 단일 OS 프로세스에서 실행되는 경우 이 확인란을 선택합니다. 테스트 드라이버가 추가 프로세스를 생성하면 지웁을 지웁습니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [Microsoft 보안 위험 검색 YAML 옵션을](yaml-configuration.md#microsoft-security-risk-detection-task) 확인하십시오.

## <a name="roslyn-analyzers-task"></a>로슬린 분석기 작업

> [!NOTE]
> Roslyn 분석기 작업을 실행하려면 빌드가 다음 조건을 충족해야 합니다.
>
> - 빌드 정의에는 C# 또는 Visual Basic 코드를 컴파일하기 위한 기본 제공 MSBuild 또는 VSBuild 빌드 작업이 포함됩니다. 분석기 작업은 기본 제공 작업의 입력 및 출력에 의존하여 Roslyn 분석기를 사용하도록 설정한 MSBuild 컴파일을 실행합니다.
> - 이 빌드 작업을 실행하는 빌드 에이전트에는 컴파일러 버전 2.6 이상에 사용할 수 있도록 Visual Studio 2017 버전 15.5 이상이 설치되어 있습니다.

작업 구성에 대한 자세한 내용은 다음 목록과 참고 사항에 나와 있습니다.

사용 가능한 옵션은 다음과 같습니다.

- **규칙 집합**: 값은 **SDL 필수,** **SDL 권장**또는 사용자 지정 규칙 집합입니다.
- **분석기 버전**: **최신**.
- **컴파일러 경고 억제 파일**: 경고 아이디 목록이 있는 텍스트 파일입니다.
- **제어 옵션** > **이 작업 실행**: 작업이 실행되는 시기를 지정합니다. **사용자 지정 조건을** 선택하여 보다 복잡한 조건을 지정합니다.

> [!NOTE]
>
> - Roslyn 분석기는 컴파일러와 통합되며 csc.exe 컴파일의 일부로만 실행할 수 있습니다. 따라서 이 작업에는 빌드에서 이전에 실행된 컴파일러 명령을 다시 재생하거나 다시 실행해야 합니다. 이 재생 또는 실행은 MSBuild 빌드 작업 로그에 대한 VSTS(Visual Studio Team Services)를 쿼리하여 수행됩니다.
>
>   빌드 정의에서 MSBuild 컴파일 명령줄을 안정적으로 얻을 수 있는 다른 방법은 없습니다. 사용자가 명령줄에 입력할 수 있도록 자유형 텍스트 상자를 추가하는 것을 고려했습니다. 그러나 이러한 명령 줄을 기본 빌드와 동기화상태로 유지하는 것은 어려울 것입니다.
>
>   사용자 지정 빌드에는 컴파일러 명령뿐만 아니라 전체 명령 집합을 재생해야 합니다. 이러한 경우 Roslyn 분석기를 사용하도록 설정하는 것은 간단하거나 신뢰할 수 없습니다.
>
> - Roslyn 분석기는 컴파일러와 통합됩니다. 로슬린 분석기는 호출하려면 컴파일이 필요합니다.
>
>   이 새 빌드 작업은 이미 빌드된 C# 프로젝트를 다시 컴파일하여 구현됩니다. 새 태스크는 MSBuild 및 VSBuild 빌드 작업만 원래 작업과 동일한 빌드 또는 빌드 정의에서 사용합니다. 그러나 이 경우 새 작업은 Roslyn 분석기를 사용하도록 설정한 상태로 사용합니다.
>
>   새 작업이 원래 작업과 동일한 에이전트에서 실행되는 경우 새 작업의 출력은 *s* 소스 폴더에서 원래 작업의 출력을 덮어씁니다. 빌드 출력은 동일하지만 MSBuild를 실행하고 아티팩트 스테이징 디렉토리에 출력을 복사한 다음 Roslyn 분석기를 실행하는 것이 좋습니다.

Roslyn 분석기 작업에 대한 추가 리소스는 Microsoft 문서의 [로슬린 기반 분석기를](https://docs.microsoft.com/dotnet/standard/analyzers/) 참조하십시오.

NuGet 페이지 [Microsoft.CodeAnalysis.FxCop분석기에서](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)이 빌드 작업에서 설치및 사용되는 분석기 패키지를 찾을 수 있습니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [로슬린 분석기 YAML 옵션을](yaml-configuration.md#roslyn-analyzers-task) 확인하십시오.

## <a name="tslint-task"></a>TSLint 작업

TSLint에 대한 자세한 내용은 [TSLint GitHub 리포지토리로](https://github.com/palantir/tslint)이동하십시오.

>[!NOTE] 
>당신이 알고 있을 수 있습니다., [TSLint GitHub 리포지토리](https://github.com/palantir/tslint) 홈 페이지는 TSLint 언젠가 에 더 이상 될 것 이라고 2019. 마이크로소프트는 [ESLint를](https://github.com/eslint/eslint) 대체 작업으로 조사 하 고 있습니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [TSLint YAML 옵션을](yaml-configuration.md#tslint-task) 확인하십시오.

## <a name="publish-security-analysis-logs-task"></a>보안 분석 로그 작업 게시

작업 구성에 대한 자세한 내용은 다음 스크린샷 및 목록에 표시됩니다.

![보안 분석 로그 빌드 작업 게시 구성](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **아티팩트 이름**: 모든 문자열 식별자입니다.
- **아티팩트 유형**: 선택에 따라 Azure DevOps Server 또는 빌드 에이전트가 액세스할 수 있는 공유 파일에 로그를 게시할 수 있습니다.
- **도구**: 특정 도구에 대한 로그를 보존하도록 선택하거나 **모든 로그를** 보존하기 위해 모든 도구를 선택할 수 있습니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [보안 로그 YAML 게시 옵션을](yaml-configuration.md#publish-security-analysis-logs-task) 확인하십시오.

## <a name="security-report-task"></a>보안 보고서 작업

보안 보고서 구성에 대한 자세한 내용은 다음 스크린샷 및 목록에 나와 있습니다.

![보안 보고서 빌드 작업 구성](./media/security-tools/4-createsecurityanalysisreport600.png)

- **보고서**: 파이프라인 **콘솔,** **TSV 파일**및 **Html 파일** 형식 중 어느 쪽을 선택합니다. 선택한 각 형식에 대해 하나의 보고서 파일이 만들어집니다.
- **도구**: 검색된 문제에 대한 요약을 원하는 빌드 정의에서 도구를 선택합니다. 선택한 각 도구에 대해 오류만 표시되는지 또는 요약 보고서에 오류와 경고가 모두 표시되는지 여부를 선택하는 옵션이 있을 수 있습니다.
- **고급 옵션**: 선택한 도구 중 하나에 대한 로그가 없는 경우 경고 또는 오류를 기록하도록 선택할 수 있습니다. 오류를 기록하면 작업이 실패합니다.
- **기본 로그 폴더**: 로그를 찾을 수 있는 기본 로그 폴더를 사용자 지정할 수 있습니다. 그러나 이 옵션은 일반적으로 사용되지 않습니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [보안 보고서 YAML 옵션을](yaml-configuration.md#security-report-task) 확인하십시오.

## <a name="post-analysis-task"></a>분석 후 작업

작업 구성에 대한 자세한 내용은 다음 스크린샷 및 목록에 표시됩니다.

![분석 후 빌드 작업 구성](./media/security-tools/a-post-analysis600.png)

- **도구**: 빌드 중단을 조건부로 삽입할 빌드 정의에서 도구를 선택합니다. 선택한 각 도구에 대해 오류만 중단할지 아니면 오류와 경고 모두에서 중단할지 여부를 선택할 수 있는 옵션이 있을 수 있습니다.
- **보고서**: 선택적으로 빌드 중단을 일으키는 결과를 작성할 수 있습니다. 결과는 Azure DevOps 콘솔 창 및 로그 파일에 기록됩니다.
- **고급 옵션**: 선택한 도구 중 하나에 대한 로그가 없는 경우 경고 또는 오류를 기록하도록 선택할 수 있습니다. 오류를 기록하면 작업이 실패합니다.

이 작업에 대한 YAML 구성에 대한 자세한 내용은 [사후 분석 YAML 옵션을](yaml-configuration.md#post-analysis-task) 확인하십시오.

## <a name="next-steps"></a>다음 단계

YAML 기반 구성에 대한 자세한 내용은 [YAML 구성 가이드를](yaml-configuration.md)참조하십시오.

보안 코드 분석 확장 및 제공되는 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지를](security-code-analysis-faq.md)확인하십시오.
