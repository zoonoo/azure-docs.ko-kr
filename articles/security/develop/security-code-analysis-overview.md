---
title: Microsoft 보안 코드 분석 설명서 개요
description: Microsoft 보안 코드 분석 확장에 대해 알아봅니다. 이 확장을 사용 하 여 Azure DevOps CI/ID 파이프라인에 보안 코드 분석을 추가할 수 있습니다.
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
ms.openlocfilehash: af4797d7d7d7cb866674747ea8871bebb059207d
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031248"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석 정보

Microsoft 보안 코드 분석 확장을 통해 팀은 Azure DevOps 지속적인 통합 및 배달 (CI/CD) 파이프라인에 보안 코드 분석을 추가할 수 있습니다. 이 분석은 Microsoft의 [SDL (보안 개발 수명 주기)](https://www.microsoft.com/securityengineering/sdl/practices) 전문가가 권장 합니다.

일관 된 UX는 실행 중인 도구의 복잡성을 숨겨 보안을 간소화 합니다. NuGet 기반 도구 제공 기능을 사용 하 여 팀은 더 이상 도구 설치 또는 업데이트를 관리할 필요가 없습니다. 빌드 작업에 대 한 명령줄 및 기본 인터페이스를 모두 사용 하 여 모든 사용자는 원하는 만큼 도구를 제어할 수 있습니다.

팀은 다음과 같은 강력한 후 처리 기능을 사용할 수도 있습니다.

- 보존을 위한 로그 게시
- 실행 가능한 개발자 중심의 보고서를 생성 합니다.
- 재발 테스트에 대 한 빌드 중단 구성.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석을 사용 해야 하는 이유는 무엇 인가요?

### <a name="security-simplified"></a>보안 간소화

Azure DevOps 파이프라인에 Microsoft 보안 코드 분석 도구를 추가 하는 작업은 새로운 작업을 추가 하는 것 만큼 간단 합니다. 작업을 사용자 지정 하거나 기본 동작을 사용 합니다. 작업은 Azure DevOps 파이프라인의 일부로 실행 되며 많은 종류의 결과를 설명 하는 로그를 생성 합니다.

### <a name="clean-builds"></a>빌드 정리

도구에서 보고 한 초기 문제를 해결 한 후 확장을 구성 하 여 새 문제에 대 한 빌드를 중단할 수 있습니다.모든 끌어오기 요청에 연속 통합 빌드를 설정 하는 것이 쉽습니다.

### <a name="set-it-and-forget-it"></a>설정 하 고 잊어버린 경우

기본적으로 빌드 작업 및 도구는 최신 상태로 유지 됩니다. 업데이트 된 버전의 도구가 있는 경우이를 다운로드 하 여 설치할 필요가 없습니다. 확장은 사용자를 위해 업데이트를 처리 합니다.

### <a name="under-the-hood"></a>기본적인 이해

확장의 빌드 작업은의 복잡성을 숨깁니다.
  - 보안 정적 분석 도구를 실행 하 고 있습니다.
  - 로그 파일의 결과를 처리 하 여 요약 보고서를 만들거나 빌드를 중단 합니다.

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft 보안 코드 분석 도구 집합

Microsoft 보안 코드 분석 확장을 통해 최신 버전의 중요 한 분석 도구를 쉽게 사용할 수 있습니다. 확장에는 Microsoft에서 관리 하는 도구와 오픈 소스 도구가 모두 포함 됩니다.

이러한 도구는 해당 빌드 작업을 사용 하 여 파이프라인을 구성 하 고 실행 한 후에 클라우드 호스팅 에이전트로 자동으로 다운로드 됩니다.

이 섹션에는 확장에서 현재 사용할 수 있는 도구 집합이 나열 되어 있습니다. 더 많은 도구를 추가 하는 것을 시청 하세요. 또한 추가 하고자 하는 도구에 대 한 제안을 보내 주시기 바랍니다.

### <a name="anti-malware-scanner"></a>맬웨어 방지 스캐너

맬웨어 방지 스캐너 빌드 작업은 이제 Microsoft 보안 코드 분석 확장에 포함 되어 있습니다. Windows Defender가 이미 설치 되어 있는 빌드 에이전트에서이 작업을 실행 해야 합니다. 자세한 내용은 [Windows Defender 웹 사이트](https://aka.ms/defender)를 참조 하십시오.

### <a name="binskim"></a>BinSkim

BinSkim는 이진 파일의 컴파일러 설정, 링커 설정 및 기타 보안 관련 특성의 유효성을 검사 하는 PE (이식 가능한 실행) 경량 스캐너입니다. 이 빌드 작업은 binskim.exe 콘솔 응용 프로그램에 대 한 명령줄 래퍼를 제공 합니다. BinSkim는 오픈 소스 도구입니다. 자세한 내용은 [GitHub의 BinSkim](https://github.com/Microsoft/binskim)를 참조 하세요.

### <a name="credential-scanner"></a>자격 증명 스캐너

소스 코드에 저장 된 암호 및 기타 암호는 심각한 문제입니다. 자격 증명 스캐너는이 문제를 해결 하는 데 도움이 되는 독점적인 정적 분석 도구입니다. 이 도구는 소스 코드와 빌드 출력의 자격 증명, 암호, 인증서 및 기타 중요 한 콘텐츠를 검색 합니다.

### <a name="roslyn-analyzers"></a>Roslyn 분석기

Roslyn 분석기는 관리 되는 c # 및 Visual Basic 코드를 정적으로 분석 하기 위한 Microsoft의 컴파일러 통합 도구입니다. 자세한 내용은 [Roslyn 기반 분석기](https://docs.microsoft.com/dotnet/standard/analyzers/api-analyzer)를 참조 하세요.

### <a name="tslint"></a>TSLint

TSLint는 TypeScript 코드에서 가독성, 관리 용이성 및 기능에 대 한 오류를 확인 하는 확장 가능한 정적 분석 도구입니다. 최신 편집기 및 빌드 시스템에서 광범위 하 게 지원 됩니다. 자신의 보풀이 아닌 규칙, 구성 및 포맷터를 사용 하 여 사용자 지정할 수 있습니다. TSLint는 오픈 소스 도구입니다. 자세한 내용은 [GitHub의 TSLint](https://github.com/palantir/tslint)를 참조 하세요.

## <a name="analysis-and-post-processing-of-results"></a>결과 분석 및 사후 처리

Microsoft 보안 코드 분석 확장에는 세 가지 후 처리 작업도 있습니다. 이러한 작업을 통해 보안 도구 작업에서 찾은 결과를 분석할 수 있습니다. 이러한 작업은 파이프라인에 추가 될 때 일반적으로 다른 모든 도구 작업을 따릅니다.

### <a name="publish-security-analysis-logs"></a>보안 분석 로그 게시

보안 분석 로그 빌드 빌드 태스크는 빌드 중에 실행 되는 보안 도구의 로그 파일을 유지 합니다. 조사 및 추가 작업을 위해 이러한 로그를 읽을 수 있습니다.

로그 파일을 Azure Artifacts에 .zip 파일로 게시할 수 있습니다. 개인 빌드 에이전트에서 액세스할 수 있는 파일 공유에 복사할 수도 있습니다.

### <a name="security-report"></a>보안 보고서

보안 보고서 작성 태스크는 로그 파일을 구문 분석 합니다. 이러한 파일은 빌드 중에 실행 되는 보안 도구에 의해 생성 됩니다. 그런 다음, 빌드 작업은 단일 요약 보고서 파일을 만듭니다. 이 파일은 분석 도구에서 발견 한 모든 문제를 표시 합니다.

특정 도구나 모든 도구에 대 한 결과를 보고 하도록이 작업을 구성할 수 있습니다. 오류 또는 오류 및 경고와 같은 보고할 문제 수준을 선택할 수도 있습니다.

### <a name="post-analysis-build-break"></a>사후 분석 (빌드 중단)

사후 분석 빌드 작업을 사용 하 여 빌드 실패를 의도적으로 발생 시키는 빌드 중단을 삽입할 수 있습니다. 하나 이상의 분석 도구에서 코드에 문제를 보고 하는 경우 빌드 중단을 삽입 합니다.

특정 도구나 모든 도구에서 발견 한 문제에 대해 빌드를 중단 하도록이 작업을 구성할 수 있습니다. 오류 또는 경고와 같이 발견 된 문제의 심각도에 따라 구성할 수도 있습니다.

>[!NOTE]
>기본적으로 작업이 성공적으로 완료 되 면 각 빌드 작업이 성공 합니다. 이는 도구에서 문제를 발견 하 여 모든 도구를 실행할 수 있도록 하 여 빌드가 완료 될 때까지 실행 될 수 있는지 여부에 해당 합니다.

## <a name="next-steps"></a>다음 단계

Microsoft 보안 코드 분석을 등록 하 고 설치 하는 방법에 대 한 지침은 [온 보 딩 및 설치 가이드](security-code-analysis-onboard.md)를 참조 하세요.

빌드 작업을 구성 하는 방법에 대 한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 또는 [yaml 구성 가이드](yaml-configuration.md)를 참조 하세요.

확장 및 제공 된 도구에 대 한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인 하세요.
