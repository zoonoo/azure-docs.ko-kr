---
title: Azure Security Center란? | Microsoft Docs
description: Azure Security Center, 주요 기능 및 작동 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 1b0a6c6d6daa686404021afb2e10b25bb9f54191
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319390"
---
# <a name="what-is-azure-security-center"></a>Azure Security Center란?
Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 제공합니다. Security Center를 사용하여 워크로드에 보안 정책을 적용하고, 위협에 대한 노출을 제한하고, 공격을 검색 및 대응할 수 있습니다.

보안 센터를 사용해야 하는 이유

- **중앙 집중식 정책 관리** – 모든 하이브리드 클라우드 작업에 걸쳐 보안 정책을 중앙에서 관리하여 회사 또는 규정 보안 요구 사항을 준수할 수 있습니다.
- **연속적인 보안 평가** – 머신, 네트워크, 저장소 및 데이터 서비스, 응용 프로그램의 보안 상태를 모니터링하여 잠재적인 보안 문제를 찾아낼 수 있습니다.
- **실행 가능한 권장 지침** – 우선 순위가 지정된 실행 가능한 보안 권장 지침을 사용하여 공격자들이 악용하기 전에 보안 취약성을 수정할 수 있습니다.
- **경고 및 인시던트 우선 순위 지정** - 우선 순위가 지정된 보안 경고와 인시던트를 통해 가장 중요한 위협부터 중점적으로 확인할 수 있습니다.
- **고급 클라우드 방어** – 관리 포트 및 적응형 응용 프로그램 제어에 대한 Just-In-Time 액세스를 통해 VM에서 실행되는 응용 프로그램을 제어함으로써 위협을 줄일 수 있습니다.
- **통합 보안 솔루션** - 연결된 파트너 솔루션을 비롯한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.

**Security Center - 개요**에서는 Azure 및 비 Azure 작업의 보안 태세를 빠르게 확인하여 작업의 보안을 검색 및 평가하고 위험을 파악 및 완화할 수 있습니다. 기본 제공 대시보드는 주의가 필요한 보안 경고 및 취약성에 대한 즉각적인 정보를 제공합니다.

![개요][1]

## <a name="centralized-policy-management"></a>중앙 집중식 정책 관리
**정책 및 준수** 섹션(위에 표시됨)은 구독 적용 범위, 정책 준수 및 보안 상태에 대한 빠른 정보를 제공합니다.

### <a name="subscription-coverage"></a>구독 적용 범위
이 섹션에는 액세스 권한(읽기 또는 쓰기)이 있는 총 구독 수 및 구독이 실행 중인 Security Center 적용 범위 수준(표준 또는 무료)이 표시됩니다.

- **적용(표준)** - 적용된 구독이 Security Center에서 제공하는 최대 수준의 보호 하에 실행 중입니다.
- **적용(무료)** - 적용된 구독이 Security Center에서 제공하는 제한된 무료 수준의 보호 하에 실행 중입니다.
- **적용되지 않음** - 이 상태의 구독은 Security Center에서 모니터링되지 않습니다.

차트를 선택하면 **적용 범위** 창이 열립니다. 탭(**적용되지 않음**, **기본 적용 범위** 또는 **표준 적용 범위**)을 선택하면 각 상태의 구독 목록이 제공됩니다. 탭 중 하나에서 구독을 선택하면 구독에 대한 추가 정보가 제공됩니다. 이 정보를 사용하여 구독 소유자를 식별하고 Security Center를 사용하도록 설정하거나 구독 적용 범위를 늘리기 위해 구독 소유자에게 연락할 수 있습니다.

![Security Center 적용 범위][9]

### <a name="policy-compliance"></a>정책 준수
정책 준수는 할당된 모든 정책의 준수 요소에 의해 결정됩니다. 관리 그룹, 구독 또는 작업 영역의 전반적인 준수 점수는 할당의 가중 평균입니다. 단일 할당의 정책 수 및 할당이 적용되는 리소스 수에서 가중치 평균 비율입니다.

예를 들어 사용자 구독에 할당된 5개의 정책이 포함된 두 개의 VM 및 한 개의 이니셔티브가 있으면 구독에 10개의 평가가 있습니다. VM 중 하나가 두 개의 정책을 준수하지 않는 경우 구독 할당의 전반적인 준수 점수는 80%입니다.

이 섹션에는 전반적인 준수 비율 및 최소 규격 구독이 표시됩니다. **작업 환경에 대한 정책 준수 표시**를 선택하면 **정책 관리** 창이 열립니다. **정책 관리**에는 관리 그룹, 구독 및 작업 영역의 계층 구조가 표시됩니다. 여기에서 구독 또는 관리 그룹을 선택하여 보안 정책을 관리합니다.

![정책 관리][10]

보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Security Center에서 정책을 정의하고 워크로드의 유형 또는 데이터의 민감도에 맞게 조정하여 Security Center에서 모니터링 및 추천하는 제어를 결정합니다. 관리 그룹 또는 구독을 클릭하여 Security Center에서 보안 정책을 편집할 수 있습니다. Azure Policy를 사용하여 새 정의를 만들고, 추가 정책을 정의하고, 관리 그룹에 걸쳐 정책을 할당할 수도 있습니다.

**설정 편집 >** 을 선택하여 구독, 관리 그룹, 리소스 그룹 또는 작업 영역 수준에서 다음 Security Center 설정을 편집합니다.

- **데이터 컬렉션**: 에이전트 프로비전 및 보안 [데이터 컬렉션](security-center-enable-data-collection.md) 설정을 결정합니다.
- **전자 메일 알림**: 보안 연락처 및 [전자 메일 알림](security-center-provide-security-contact-details.md) 설정을 결정합니다.
- **가격 책정 계층**: 무료 또는 표준 [가격 선택](security-center-pricing.md)을 정의합니다. 선택한 계층에 따라 범위의 리소스에 대해 사용할 수 있는 Security Center 기능이 결정됩니다.
- **보안 구성 편집**: Security Center에서 평가한 OS 구성을 보고 수정하여 잠재적인 보안 취약성을 식별할 수 있습니다.

자세한 내용은 [Azure Policy를 사용하여 Security Center 보안 정책 통합](security-center-azure-policy.md)을 참조하세요.

### <a name="manage-and-govern-your-security-posture"></a>보안 상태 관리 및 제어
**정책 및 준수** 아래 대시보드 오른쪽에는 전반적인 보안 상태를 즉시 개선하기 위해 적용할 수 있는 인사이트가 제공됩니다. 예:

- 보안 표준에 대한 준수를 검토 및 추적하기 위해 Security Center 정책 정의 및 할당
- SIEM 커넥터에서 Security Center 보안 경고 만들기
- 시간에 따른 정책 준수

## <a name="continuous-security-assessment"></a>지속적인 보안 평가
**Security Center - 개요** 아래 리소스 보안 예방 조치 섹션에서는 식별된 문제 수 및 각 리소스 종류의 보안 상태를 표시하는 리소스 보안 예방 조치의 빠른 보기를 제공합니다. 지속적인 평가를 통해 보안 업데이트가 누락된 시스템이나 노출된 네트워크 포트와 같은 잠재적인 보안 문제를 찾아낼 수 있습니다.

### <a name="secure-score"></a>보안 점수
Azure Security Center 보안 점수는 보안 권장 사항을 검토하고 해당 권장 사항에 우선 순위를 지정하므로 우선 수행할 권장 사항을 알 수 있어 조사 우선 순위를 지정할 수 있도록 가장 심각한 보안 취약성을 찾는 데 도움이 됩니다. 보안 점수는 보안 워크로드를 달성하기 위해 보안을 강화하는 데 도움이 되는 측정 도구입니다. 자세한 내용은 [Azure Security Center의 보안 점수](security-center-secure-score.md)를 참조하세요.

### <a name="health-monitoring"></a>상태 모니터링
**리소스 상태 모니터링**에서 리소스 종류를 선택하면 리소스 및 식별된 모든 취약성의 목록이 제공됩니다. 리소스 종류는 계산 및 응용 프로그램, 네트워킹, 데이터 및 저장소, ID 및 액세스입니다.

**계산 및 앱**을 선택했습니다. **Compute** 아래 탭이 4개 있습니다.

- **개요**: Security Center에서 파악된 모니터링 내용 및 권장 사항입니다.
- **VM 및 컴퓨터**: VM과 컴퓨터 목록 및 현재 보안 상태 입니다.
- **Cloud Services**: Security Center에서 모니터링되는 웹 및 작업자 역할의 목록입니다.
- **App Services(미리 보기)**: 웹 응용 프로그램 및 App Service 환경과 각 환경의 현재 보안 상태가 표시되는 목록입니다.

![보안 상태 모니터링][3]

자세한 내용은 [보안 상태 모니터링](security-center-monitoring.md)을 참조하세요.

## <a name="actionable-recommendations"></a>실행 가능한 권장 사항
Security Center는 Azure 및 비 Azure 리소스의 보안 상태를 분석하여 잠재적인 보안 취약성을 식별합니다. **리소스**에서 **권장 사항**을 선택하면 보안 문제 해결 과정을 안내하는 우선 순위가 지정된 보안 권장 사항 목록이 제공됩니다.

![권장 사항][4]

자세한 내용은 [보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.

### <a name="most-prevalent-recommendations"></a>가장 일반적인 권장 사항
**리소스** 아래 대시보드의 오른쪽에는 가장 많은 리소스에 적용되는 가장 일반적인 권장 사항 목록이 제공됩니다. 가장 일반적인 권장 사항은 주의를 집중해야 하는 위치를 강조 표시합니다. 오른쪽 화살표를 선택하면 영향이 가장 높은 권장 사항이 제공됩니다.

![가장 일반적인 권장 사항][11]

이는 사용자 환경에서 영향이 가장 높은 권장 사항입니다. 이 권장 사항을 해결하면 규정 준수가 최고 수준으로 향상됩니다. 이 예제에서 권장 사항은 “디스크 암호화 적용”입니다. **규정 준수 향상**을 선택하면 권장 사항의 설명 및 영향을 받는 리소스 목록이 제공됩니다.

![디스크 암호화 적용][12]

## <a name="threat-protection"></a>위협 보호
이 영역에서는 리소스에서 발견된 보안 경고 및 해당 경고의 심각도 수준을 시각화합니다.

### <a name="prioritized-alerts-and-incidents"></a>우선 순위가 지정된 경고 및 인시던트
Security Center에서는 고급 분석 및 전역 위협 인텔리전스를 사용하여 들어오는 공격 및 위반 후 활동을 감지합니다. 경고는 우선 순위가 지정된 후 인시던트로 그룹화되므로, 가장 중요한 위협에 우선적으로 집중할 수 있습니다. 사용자 지정 보안 경고를 만들 수도 있습니다.

**심각도별 보안 경고** 또는 **시간에 따른 보안 경고**를 선택하면 경고에 대한 자세한 내용이 제공됩니다.

![우선 순위가 지정된 경고 및 인시던트][7]

시각적, 대화형 조사 경험을 통해 공격의 범위 및 영향을 빠르게 평가하고, 미리 정의된 쿼리나 임시 쿼리를 통해 보안 데이터를 보다 상세하게 탐색할 수 있습니다.

자세한 내용은 [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.

대시보드 오른쪽에는 먼저 해결해야 할 경고의 우선 순위를 지정하고 일반적인 취약성의 위치를 파악하는 데 도움이 되는 정보가 제공됩니다.

- **가장 공격받은 리소스**: 경고 수가 가장 많은 특정 리소스
- **가장 일반적인 경고**: 가장 많은 리소스에 영향을 주는 경고 형식

## <a name="just-in-time-vm-access"></a>Just-In-Time VM 액세스
Azure VM의 관리 포트에 대한 제어되는 Just-In-Time 액세스를 사용하여 네트워크 공격에 대한 취약성을 낮춤으로써 무차별 암호 대입 공격(brute force attack) 및 기타 네트워크 공격에 대한 노출을 크게 줄일 수 있습니다.

![Just-In-Time VM 액세스][5]

사용자가 가상 머신에 연결할 수 있는 방법에 대한 규칙을 지정합니다. 필요한 경우 Security Center에서 또는 PowerShell을 통해 액세스를 요청할 수 있습니다. 요청이 규칙에 부합되기만 하면, 요청된 시간 동안 액세스 권한이 자동으로 부여됩니다.

자세한 내용은 [Just-In-Time를 사용하여 가상 머신 액세스 관리](security-center-just-in-time.md)를 참조하세요.

## <a name="adaptive-application-controls"></a>적응 응용 프로그램 컨트롤
Machine Learning을 통해 제공되며 특정 Azure 작업에 맞게 조정되는 허용 목록 권장 사항을 적용하여 맬웨어 및 기타 원치 않는 응용 프로그램을 차단할 수 있습니다.

![적응 응용 프로그램 컨트롤][6]

Security Center에서 생성된 권장되는 응용 프로그램 허용 목록 규칙을 검토한 후 클릭하여 적용하거나 이미 구성된 규칙을 편집합니다.

자세한 내용은 [적응형 응용 프로그램 제어](security-center-adaptive-application.md)를 참조하세요.

## <a name="integrate-your-security-solutions"></a>보안 솔루션 통합
Security Center에서 네트워크 방화벽과 같은 연결된 파트너 솔루션 및 기타 Microsoft 서비스를 비롯한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.

![보안 솔루션 통합][8]

자세한 내용은 [보안 솔루션 통합](security-center-partner-integration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 보안 센터를 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- Security Center의 무료 가격 책정 계층은 Azure 구독을 사용하여 사용하도록 설정됩니다. 고급 보안 관리 및 위협 검색 기능을 활용하려면 표준 가격 책정 계층으로 업그레이드해야 합니다. 표준 계층은 처음 60일 동안 무료입니다. 자세한 내용은 [Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.
- 지금 Security Center 표준을 설정할 준비가 된 경우 [빠른 시작: Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md)의 지침을 따릅니다.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
