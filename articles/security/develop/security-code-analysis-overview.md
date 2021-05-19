---
title: Microsoft 보안 코드 분석 설명서 개요
description: Microsoft 보안 코드 분석 확장에 대해 알아봅니다. 이 확장을 사용하여 Azure DevOps CI/ID 파이프라인에 보안 코드 분석을 추가할 수 있습니다.
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
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801199"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석 정보

> [!Note]
> 2022년 3월 1일부터 MSCA(Microsoft 보안 코드 분석) 확장이 사용 중지됩니다. 기존 MSCA 고객은 2022년 3월 1일까지 MSCA에 대한 액세스가 유지됩니다. Azure DevOps의 대체 옵션은 [OWASP 소스 코드 분석 도구](https://owasp.org/www-community/Source_Code_Analysis_Tools)를 참조하세요. GitHub로 마이그레이션을 계획하는 고객의 경우 [Github 고급 보안](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)을 확인할 수 있습니다.

Microsoft 보안 코드 분석 확장을 통해 팀은 Azure DevOps CI/CD(연속 통합 및 업데이트) 파이프라인에 보안 코드 분석을 추가할 수 있습니다. 이 분석은 Microsoft의 [SDL(보안 개발 수명 주기)](https://www.microsoft.com/securityengineering/sdl/practices) 전문가가 권장합니다.

일관된 UX는 도구 실행의 복잡성을 숨겨 보안을 단순화합니다. 도구의 NuGet 기반 배달을 통해 팀은 더 이상 도구 설치 또는 업데이트를 관리할 필요가 없습니다. 빌드 작업을 위한 명령줄 및 기본 인터페이스를 모두 사용하여 모든 사용자가 원하는 만큼 도구를 제어할 수 있습니다.

팀은 다음과 같은 강력한 사후 처리 기능을 사용할 수도 있습니다.

- 보존을 위한 로그 게시
- 작업 가능한 개발자 중심의 보고서 생성
- 재발 테스트에 대한 빌드 중단 구성

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석을 사용해야 하는 이유는 무엇인가요?

### <a name="security-simplified"></a>간소화된 보안

Azure DevOps 파이프라인에 Microsoft 보안 코드 분석 도구를 추가하는 것은 새 작업을 추가하는 것만큼 간단합니다. 작업을 사용자 지정하거나 기본 동작을 사용합니다. 작업은 Azure DevOps 파이프라인의 일부로 실행되며 많은 종류의 결과를 설명하는 로그를 생성합니다.

### <a name="clean-builds"></a>클린 빌드

도구에서 보고한 초기 문제를 해결한 후 확장을 구성하여 새 문제에 대한 빌드를 중단할 수 있습니다. 모든 끌어오기 요청에 연속 통합 빌드를 설정하는 것이 쉽습니다.

### <a name="set-it-and-forget-it"></a>설정하고 잊어버린 경우

기본적으로 빌드 작업 및 도구는 최신 상태로 유지됩니다. 업데이트된 버전의 도구가 있는 경우 이를 다운로드하여 설치할 필요가 없습니다. 확장이 업데이트를 처리합니다.

### <a name="under-the-hood"></a>기본적인 이해

확장의 빌드 작업이 다음과 같은 복잡성을 숨깁니다.
  - 보안 정적 분석 도구 실행
  - 로그 파일의 결과를 처리하여 요약 보고서를 생성하거나 빌드 중단

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft 보안 코드 분석 도구 집합

Microsoft 보안 코드 분석 확장을 통해 최신 버전의 중요한 분석 도구를 쉽게 사용할 수 있습니다. 확장에는 Microsoft 관리 도구와 오픈 소스 도구가 모두 포함됩니다.

이러한 도구는 해당 빌드 작업을 사용하여 파이프라인을 구성하고 실행한 후에 클라우드 호스트된 에이전트로 자동으로 다운로드됩니다.

이 섹션에는 현재 확장에서 사용할 수 있는 도구 집합이 나열되어 있습니다. 더 많은 도구가 추가되었는지 확인합니다. 또한 추가할 도구에 대한 제안을 보내주세요.

### <a name="anti-malware-scanner"></a>맬웨어 방지 스캐너

맬웨어 방지 스캐너 빌드 작업은 이제 Microsoft 보안 코드 분석 확장에 포함되어 있습니다. Windows Defender가 이미 설치되어 있는 빌드 에이전트에서 이 작업을 실행해야 합니다. 자세한 내용은 [Windows Defender 웹 사이트](https://aka.ms/defender)를 참조하세요.

### <a name="binskim"></a>BinSkim

BinSkim은 이진 파일의 컴파일러 설정, 링커 설정 및 기타 보안 관련 특성을 검증하는 PE(이식 가능) 경량 스캐너입니다. 이 빌드 작업은 binskim.exe 콘솔 애플리케이션에 대한 명령줄 래퍼를 제공합니다. BinSkim은 오픈 소스 도구입니다. 자세한 내용은 [GitHub의 BinSkim](https://github.com/Microsoft/binskim)을 참조하세요.

### <a name="credential-scanner"></a>자격 증명 스캐너

소스 코드에 저장된 암호 및 기타 비밀은 중요한 문제입니다. 자격 증명 스캐너는 이 문제를 해결하는 데 도움이 되는 독점적인 정적 분석 도구입니다. 이 도구는 소스 코드와 빌드 출력에서 자격 증명, 비밀, 인증서 및 기타 민감한 콘텐츠를 검색합니다.

### <a name="roslyn-analyzers"></a>Roslyn 분석기

Roslyn 분석기는 관리되는 C# 및 Visual Basic 코드를 정적으로 분석하기 위한 Microsoft의 컴파일러 통합 도구입니다. 자세한 내용은 [Roslyn 기반 분석기](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings)를 참조하세요.

### <a name="tslint"></a>TSLint

TSLint는 TypeScript 코드의 가독성, 관리 용이성 및 기능에 대한 오류를 확인하는 확장 가능한 정적 분석 도구입니다. 최신 편집기 및 빌드 시스템에서 광범위하게 지원됩니다. 고유한 Lint 규칙, 구성 및 포맷터로 사용자 지정할 수 있습니다. TSLint는 오픈 소스 도구입니다. 자세한 내용은 [GitHub의 TSLint](https://github.com/palantir/tslint)를 참조하세요.

## <a name="analysis-and-post-processing-of-results"></a>결과 분석 및 사후 처리

Microsoft 보안 코드 분석 확장에는 세 가지 사후 처리 작업도 있습니다. 이러한 작업을 통해 보안 도구 작업에서 찾은 결과를 분석할 수 있습니다. 파이프라인에 추가되면 이러한 작업은 일반적으로 다른 모든 도구 작업을 따릅니다.

### <a name="publish-security-analysis-logs"></a>보안 분석 로그 게시

보안 분석 로그 게시 빌드 작업은 빌드 중에 실행되는 보안 도구의 로그 파일을 유지합니다. 조사 및 추가 작업을 위해 이러한 로그를 읽을 수 있습니다.

로그 파일을 Azure Artifacts에 .zip 파일로 게시할 수 있습니다. 프라이빗 빌드 에이전트에서 액세스할 수 있는 파일 공유에 복사할 수도 있습니다.

### <a name="security-report"></a>보안 보고서

보안 보고서 작성 작업은 로그 파일을 구문 분석합니다. 이러한 파일은 빌드 중에 실행되는 보안 도구에 의해 생성됩니다. 그런 다음 빌드 작업은 단일 요약 보고서 파일을 만듭니다. 이 파일은 분석 도구에서 발견한 모든 문제를 표시합니다.

특정 도구나 모든 도구에 대한 결과를 보고하도록 이 작업을 구성할 수 있습니다. 오류만 또는 오류와 경고 모두와 같이 보고할 문제 수준을 선택할 수도 있습니다.

### <a name="post-analysis-build-break"></a>사후 분석(빌드 중단)

사후 분석 빌드 작업을 사용하여 빌드 실패를 의도적으로 발생시키는 빌드 중단을 삽입할 수 있습니다. 하나 이상의 분석 도구에서 코드에 문제를 보고하는 경우 빌드 중단을 삽입합니다.

특정 도구나 모든 도구에서 발견한 문제에 대해 빌드를 중단하도록 이 작업을 구성할 수 있습니다. 오류 또는 경고와 같이 발견된 문제의 심각도에 따라 구성할 수도 있습니다.

>[!NOTE]
>기본적으로 작업이 성공적으로 완료되면 각 빌드 작업이 성공합니다. 도구가 문제를 발견했는지 여부에 관계없이 모든 도구가 실행되도록 허용하여 빌드를 완료할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Microsoft 보안 코드 분석을 온보딩하고 설치하는 방법에 대한 지침은 [온보딩 및 설치 가이드](security-code-analysis-onboard.md)를 참조하세요.

빌드 작업을 구성하는 방법에 대한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 또는 [YAML 구성 가이드](yaml-configuration.md)를 참조하세요.

제공된 확장 및 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인하세요.