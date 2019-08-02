---
title: Microsoft Azure 보안 코드 분석 설명서 개요
description: 이 문서는 보안 코드 분석 확장에 대 한 개요입니다.
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718289"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석 정보

**Microsoft 보안 코드 분석 확장** 을 통해 팀은 MICROSOFT의 [SDL (보안 개발 수명 주기)](https://www.microsoft.com/securityengineering/sdl/practices) 전문가의 권장에 따라 보안 코드 분석을 Azure DEVOPS CI/CD 파이프라인에 원활 하 게 통합할 수 있습니다. 다양 한 도구를 실행 하는 복잡성을 추상화 하는 일관 된 UX를 통해 보안이 간소화 됩니다. NuGet 기반 도구를 사용 하 여 팀은 더 이상 도구 설치 또는 업데이트를 관리할 필요가 없습니다. 명령줄 및 기본 빌드 작업 인터페이스를 사용 하 여 숙련 된 도구의 전문가 모든 사용자는 일상적인 개발자에 게 원하는 만큼 도구를 거의 또는 거의 제어할 수 있습니다. 또한 팀은 보존을 위한 로그 게시, 실행 가능한 개발자 중심의 보고서 생성 & 재발에 대 한 빌드 중단을 구성 하 여 강력한 사후 처리 기능을 활용할 수 있습니다.

## <a name="why-microsoft-security-code-analysis"></a>Microsoft 보안 코드 분석

### <a name="security-simplified"></a>보안 간소화

Azure DevOps 파이프라인에 보안 코드 분석 도구를 추가 하는 작업은 새 작업을 추가 하는 것 만큼 간단 합니다. 사용자 지정 하거나 기본값으로 이동 합니다. 작업은 DevOps 파이프라인의 일부로 실행 되 고 모든 종류의 결과를 자세히 보여 주는 로그를 생성 합니다.

### <a name="clean-builds"></a>빌드 정리

도구에서 보고 한 초기 문제를 해결 한 후 확장을 구성 하 여 새 문제에 대 한 빌드를 중단할 수 있습니다. 모든 끌어오기 요청에서 연속 통합 빌드를 설정 하는 것은 쉽지 않습니다.

### <a name="set-it-and-forget-it"></a>설정 하 고 잊어버린 경우

빌드 작업 및 도구는 최신 상태로 유지 되도록 설정할 수 있습니다 (기본적으로). 업데이트 된 버전의 도구가 있는 경우이를 다운로드 하 여 설치할 필요가 없습니다. 이 확장은 사용자에 게이를 처리 합니다. 

>>>
### <a name="under-the-hood"></a>내부적으로

Microsoft 보안 코드 분석 확장 빌드 작업은 다음과 같은 복잡성을 추상화 합니다.
  - 보안 정적 분석 도구 실행
  - 로그 파일의 결과를 처리 하 여 요약 보고서를 만들거나 빌드를 중단 합니다.
>>>

## <a name="security-code-analysis-toolset"></a>보안 코드 분석 도구 집합

Microsoft 보안 코드 분석 확장을 통해 최신 버전의 중요 한 분석 도구를 쉽게 사용할 수 있습니다. 확장에는 Microsoft 내부 및 오픈 소스 도구가 모두 포함 됩니다. 이 도구는 해당 하는 빌드 작업을 사용 하 여 파이프라인을 구성 & 실행 하면 클라우드 호스팅 에이전트에 자동으로 다운로드 됩니다. 다음은 현재 확장에서 사용할 수 있는 도구 집합입니다. 더 자세히 고려 하 고 추가 될 수 있는 도구에 대 한 제안을 보내 주세요.

### <a name="anti-malware-scanner"></a>맬웨어 방지 스캐너

맬웨어 방지 스캐너 빌드 작업은 이제 Microsoft 보안 코드 분석 확장에 포함 되어 있습니다. Windows Defender가 이미 설치 되어 있는 빌드 에이전트에서 실행 해야 합니다. 자세한 내용은 [Defender 웹 사이트](https://aka.ms/defender) 를 참조 하세요. 

### <a name="binskim"></a>BinSkim

BinSkim는 컴파일러/링커 설정 및 기타 보안 관련 이진 특성의 유효성을 검사 하는 PE (이식 가능한 실행) 경량 스캐너입니다. 빌드 작업은 BinSkim 응용 프로그램에 대 한 명령줄 래퍼를 제공 합니다. BinSkim는 오픈 소스 도구 이며 자세한 내용은 [GitHub의 BinSkim](https://github.com/Microsoft/binskim) 를 참조 하세요.

### <a name="credential-scanner"></a>자격 증명 스캐너

소스 코드에 저장 된 암호 및 기타 암호는 현재 중요 한 문제입니다. 자격 증명 스캐너는 소스 코드와 빌드 출력의 자격 증명, 암호, 인증서 및 기타 중요 한 콘텐츠를 검색 하는 소유 정적 분석 도구입니다.

### <a name="microsoft-security-risk-detection"></a>Microsoft 보안 위험 검색

보안 위험 검색은 소프트웨어에서 악용 가능한 보안 버그를 식별 하기 위한 Microsoft의 고유한 클라우드 기반 퍼지 테스트 서비스입니다. 이 서비스에는 별도의 온 보 딩 프로세스가 필요 합니다. 자세한 내용은 [docs.microsoft.com의 Msrd을](https://docs.microsoft.com/security-risk-detection/) 참조 하세요.

### <a name="roslyn-analyzers"></a>Roslyn 분석기

관리 코드 (C# 및 VB)를 분석 하기 위한 Microsoft의 컴파일러 통합 정적 분석 도구입니다. 자세한 내용은 [docs.microsoft.com의 Roslyn 분석기를](https://docs.microsoft.com/dotnet/standard/analyzers/) 참조 하세요.

### <a name="tslint"></a>TSLint

TSLint는 TypeScript 코드에서 가독성, 유지 관리 및 기능 오류를 확인 하는 확장 가능한 정적 분석 도구입니다. 최신 편집기 및 빌드 시스템에서 널리 지원 되며 사용자 고유의 보풀이 아닌 규칙, 구성 및 포맷터를 사용 하 여 사용자 지정할 수 있습니다. TSLint는 오픈 소스 도구 이며 자세한 내용은 [GitHub의 TSLint](https://github.com/palantir/tslint) 를 참조 하세요.

## <a name="analysis-and-post-processing-of-results"></a>결과 분석 및 사후 처리

Microsoft 보안 코드 분석 확장에는 보안 도구 태스크에서 찾은 결과를 처리 하 고 분석 하는 데 도움이 되는 세 가지 유용한 후 처리 작업이 있습니다. 이러한 작업은 일반적으로 다른 모든 도구 작업 후에 파이프라인에 추가 됩니다.

### <a name="publish-security-analysis-logs"></a>보안 분석 로그 게시
보안 분석 로그 빌드 게시 작업은 조사 및 추가 작업을 위해 빌드 중에 실행 되는 보안 도구의 로그 파일을 유지 합니다.

이러한 파일은 Azure 서버 아티팩트 (zip 파일)에 게시 하거나 개인 빌드 에이전트에서 액세스할 수 있는 파일 공유로 복사할 수 있습니다.

### <a name="security-report"></a>보안 보고서
보안 보고서 작성 태스크는 빌드 중에 실행 되는 보안 도구에서 만든 로그 파일을 구문 분석 하 고 분석 도구에서 발견 한 모든 문제를 포함 하는 단일 요약 보고서 파일을 만듭니다.

특정 도구나 모든 도구에 대 한 결과를 보고 하도록 태스크를 구성할 수 있으며, 보고 해야 하는 문제 수준 (오류 또는 오류 및 경고)을 선택할 수도 있습니다.

### <a name="post-analysis-build-break"></a>사후 분석 (빌드 중단)
분석 후 빌드 작업을 통해 고객은 하나 이상의 분석 도구에서 코드의 결과 나 문제를 보고 하는 경우 빌드 중단을 삽입 하 고 빌드를 실패할 수 있습니다.

특정 도구나 모든 도구에 대해 발견 된 문제의 심각도 (오류 또는 경고)를 기반으로 하는 문제에 대해 빌드를 중단 하도록 작업을 구성할 수 있습니다.

>[!NOTE]
>도구를 성공적으로 완료 하는 경우에는 빌드가 완료 될 때까지 모든 도구를 실행할 수 있도록 해당 작업이 완료 될 때까지 개별 빌드 작업이 성공 합니다.

## <a name="next-steps"></a>다음 단계

보안 코드 분석을 등록 하 고 설치 하는 방법에 대 한 지침은 [온 보 딩 및 설치 가이드](security-code-analysis-onboard.md) 를 참조 하세요.

빌드 작업을 구성 하는 방법에 대 한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 를 참조 하세요.

확장 및 제공 된 도구에 대 한 추가 질문이 있는 경우 [faq 페이지](security-code-analysis-faq.md) 를 참조 하세요.