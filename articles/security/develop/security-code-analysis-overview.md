---
title: Microsoft 보안 코드 분석 문서 개요
description: 이 문서는 Microsoft 보안 코드 분석 확장에 대한 개요입니다.
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851471"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석 정보

Microsoft 보안 코드 분석 확장을 통해 팀은 Azure DevOps 의 지속적인 통합 및 제공(CI/CD) 파이프라인에 보안 코드 분석을 추가할 수 있습니다. 이 분석은 Microsoft의 [SDL(보안 개발 수명 주기)](https://www.microsoft.com/securityengineering/sdl/practices) 전문가가 권장합니다.

일관된 UX는 실행 중인 도구의 복잡성을 숨김으로써 보안을 단순화합니다. NuGet 기반 도구 배달을 통해 팀은 더 이상 툴링의 설치 또는 업데이트를 관리할 필요가 없습니다. 빌드 작업에 대한 명령줄과 기본 인터페이스를 모두 사용하면 모든 사용자가 원하는 만큼 도구를 제어할 수 있습니다.

팀에서는 다음과 같은 강력한 후처리 기능을 사용할 수도 있습니다.

- 보존을 위해 로그를 게시합니다.
- 실행 가능한 개발자 중심 보고서 생성
- 회귀 테스트에서 빌드 나누기를 구성합니다.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석을 사용해야 하는 이유는 무엇입니까?

### <a name="security-simplified"></a>보안 간소화

Azure DevOps 파이프라인에 Microsoft 보안 코드 분석 도구를 추가하는 것은 새 작업을 추가하는 것만큼 간단합니다. 작업을 사용자 지정하거나 기본 동작을 사용합니다. 작업은 Azure DevOps 파이프라인의 일부로 실행되며 다양한 종류의 결과를 자세히 설명하는 로그를 생성합니다.

### <a name="clean-builds"></a>클린 빌드

도구에서 보고한 초기 문제를 해결한 후 새 문제에 대한 빌드를 중단하도록 확장을 구성할 수 있습니다.모든 끌어오기 요청에 대해 연속 통합 빌드를 설정하는 것은 쉽습니다.

### <a name="set-it-and-forget-it"></a>그것을 설정하고 잊어 버려

기본적으로 빌드 작업 및 도구는 최신 상태로 유지됩니다. 도구의 업데이트된 버전이 있는 경우 도구를 다운로드하여 설치할 필요가 없습니다. 확장 은 당신을 위해 업데이트를 처리합니다.

### <a name="under-the-hood"></a>기본적인 이해

확장의 빌드 작업은 다음의 복잡성을 숨깁니다.
  - 보안 정적 분석 도구 실행.
  - 로그 파일에서 결과를 처리하여 요약 보고서를 만들거나 빌드를 중단합니다.

## <a name="microsoft-security-code-analysis-tool-set"></a>마이크로소프트 보안 코드 분석 도구 세트

Microsoft 보안 코드 분석 확장을 사용하면 중요한 분석 도구의 최신 버전을 쉽게 사용할 수 있습니다. 확장에는 Microsoft 관리 도구와 오픈 소스 도구가 모두 포함됩니다.

이러한 도구는 해당 빌드 작업을 사용하여 파이프라인을 구성하고 실행한 후 클라우드 호스팅 에이전트에 자동으로 다운로드됩니다.

이 섹션에서는 확장에서 현재 사용할 수 있는 도구 집합을 나열합니다. 더 많은 도구의 추가에 대 한 시계. 또한 추가하려는 도구에 대한 제안 사항을 보내주십시오.

### <a name="anti-malware-scanner"></a>맬웨어 방지 스캐너

맬웨어 방지 스캐너 빌드 작업은 이제 Microsoft 보안 코드 분석 확장에 포함됩니다. 이 작업은 Windows Defender가 이미 설치된 빌드 에이전트에서 실행되어야 합니다. 자세한 내용은 Windows [Defender 웹 사이트를](https://aka.ms/defender)참조하십시오.

### <a name="binskim"></a>빈스킴

BinSkim은 컴파일러 설정, 링커 설정 및 이진 파일의 기타 보안 관련 특성을 검증하는 PE(휴대용 실행 파일) 경량 스캐너입니다. 이 빌드 작업은 binskim.exe 콘솔 응용 프로그램 주위에 명령줄 래퍼를 제공합니다. 빈스킴은 오픈 소스 도구입니다. 자세한 내용은 [GitHub의 BinSkim을](https://github.com/Microsoft/binskim)참조하십시오.

### <a name="credential-scanner"></a>자격 증명 스캐너

소스 코드에 저장된 암호 및 기타 암호는 중요한 문제입니다. 자격 증명 스캐너는 이 문제를 해결하는 데 도움이 되는 독점적인 정적 분석 도구입니다. 이 도구는 소스 코드 및 빌드 출력에서 자격 증명, 비밀, 인증서 및 기타 중요한 콘텐츠를 검색합니다.

### <a name="microsoft-security-risk-detection"></a>마이크로소프트 보안 위험 탐지

MICROSOFT 보안 위험 감지(MSRD)는 퍼지 테스트를 위한 클라우드 기반 서비스입니다. 소프트웨어에서 악용 가능한 보안 버그를 식별합니다. 이 서비스에는 별도의 구독 및 정품 인증이 필요합니다. 자세한 내용은 [MSRD 개발자 센터를](https://docs.microsoft.com/security-risk-detection/)참조하십시오.

### <a name="roslyn-analyzers"></a>Roslyn 분석기

Roslyn 분석기는 관리되는 C# 및 시각적 기본 코드를 정적으로 분석하기 위한 Microsoft의 컴파일러 통합 도구입니다. 자세한 내용은 [Roslyn 기반 분석기를](https://docs.microsoft.com/dotnet/standard/analyzers/)참조하십시오.

### <a name="tslint"></a>트슬린트 (주)

TSLint는 TypeScript 코드를 검사하여 가독성, 유지 관리 성 및 기능 오류를 검사하는 확장 가능한 정적 분석 도구입니다. 그것은 널리 현대 편집자및 빌드 시스템에 의해 지원됩니다. 고유한 보풀 규칙, 구성 및 포터로 사용자 지정할 수 있습니다. TSLint는 오픈 소스 도구입니다. 자세한 내용은 [GitHub의 TSLint를](https://github.com/palantir/tslint)참조하십시오.

## <a name="analysis-and-post-processing-of-results"></a>결과 분석 및 사후 처리

Microsoft 보안 코드 분석 확장에는 세 가지 후처리 작업도 있습니다. 이러한 작업은 보안 도구 작업에서 찾은 결과를 분석하는 데 도움이 됩니다. 파이프라인에 추가하면 이러한 작업은 일반적으로 다른 모든 도구 작업을 따릅니다.

### <a name="publish-security-analysis-logs"></a>보안 분석 로그 게시

게시 보안 분석 로그 빌드 작업은 빌드 중에 실행되는 보안 도구의 로그 파일을 유지합니다. 조사 및 후속 조치를 위해 이러한 로그를 읽을 수 있습니다.

로그 파일을 Azure 아티팩트에 .zip 파일로 게시할 수 있습니다. 개인 빌드 에이전트에서 액세스할 수 있는 파일 공유에 복사할 수도 있습니다.

### <a name="security-report"></a>보안 보고서

보안 보고서 빌드 작업은 로그 파일을 구문 분석합니다. 이러한 파일은 빌드 중에 실행되는 보안 도구에 의해 만들어집니다. 그런 다음 빌드 태스크는 단일 요약 보고서 파일을 만듭니다. 이 파일은 분석 도구에서 찾은 모든 문제를 보여 주며, 이 파일에는 문제가 표시됩니다.

특정 도구 또는 모든 도구에 대한 결과를 보고하도록 이 작업을 구성할 수 있습니다. 오류만 또는 오류 및 경고와 같이 보고할 문제 수준을 선택할 수도 있습니다.

### <a name="post-analysis-build-break"></a>사후 분석(빌드 중단)

사후 분석 빌드 작업을 사용하면 의도적으로 빌드가 실패하게 만드는 빌드 중단을 삽입할 수 있습니다. 하나 이상의 분석 도구가 코드에서 문제를 보고하는 경우 빌드 중단을 삽입합니다.

특정 도구 또는 모든 도구에서 발견된 문제에 대해 빌드를 중단하도록 이 작업을 구성할 수 있습니다. 오류 또는 경고와 같이 발견된 문제의 심각도에 따라 구성할 수도 있습니다.

>[!NOTE]
>의도적으로 작업이 성공적으로 완료되면 각 빌드 작업이 성공합니다. 이는 도구가 문제를 찾든 그렇지 않든 모든 도구를 실행하도록 허용하여 빌드를 완료까지 실행할 수 있도록 하는 경우입니다.

## <a name="next-steps"></a>다음 단계

Microsoft 보안 코드 분석을 온보딩하고 설치하는 방법에 대한 지침은 [온보딩 및 설치 가이드를](security-code-analysis-onboard.md)참조하십시오.

빌드 작업 구성에 대한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 또는 [YAML 구성 가이드를](yaml-configuration.md)참조하십시오.

확장 프로그램 및 제공되는 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지를](security-code-analysis-faq.md)확인하십시오.
