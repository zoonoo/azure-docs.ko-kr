---
title: "Azure Security Center 소개 | Microsoft Docs"
description: "Azure 보안 센터, 주요 기능 및 작동 방법에 대해 알아봅니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 21415af0d449d639d000e07afdb4de3680a64774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-security-center"></a>Azure 보안 센터 소개
Azure 보안 센터, 주요 기능 및 작동 방법에 대해 알아봅니다.

## <a name="what-is-azure-security-center"></a>Azure 보안 센터란?
Azure Security Center는 Azure, 온-프레미스 및 기타 클라우드용으로 통합 보안 관리 및 고급 위협 보호 기능을 제공합니다.  또한 하이브리드 클라우드 작업을 확인하고 제어하는 기능, 위협에 대한 노출을 줄이는 적극적인 방어 기능, 그리고 빠르게 발전하는 사이버 공격에 대응할 수 있는 지능형 검색 기능을 제공합니다.

Security Center 개요에서는 Azure 및 비 Azure 작업의 보안 태세를 빠르게 확인하여 작업의 보안을 검색 및 평가하고 위험을 파악 및 완화할 수 있습니다.

![개요](./media/security-center-intro/security-center-intro-fig1.png)

## <a name="why-use-security-center"></a>보안 센터를 사용해야 하는 이유

**통합된 표시 유형 및 제어**

- **하이브리드 워크로드 전반의 보안 상태 이해**. 온-프레미스, Azure 및 기타 클라우드 플랫폼을 포함한 모든 하이브리드 클라우드 작업의 보안을 콘솔 하나에서 관리할 수 있습니다. 기본 제공 대시보드를 통해 확인이 필요한 보안 문제를 즉시 파악할 수 있습니다.
- **클라우드 워크로드에 대한 표시 유형**. 급변하는 클라우드 워크로드에 대응합니다. Azure 구독에서 생성된 새 리소스를 자동으로 검색하고 등록합니다.
- **중앙 집중식 정책 관리**. 모든 하이브리드 클라우드 작업에 걸쳐 보안 정책을 중앙에서 관리하여 회사 또는 규정 보안 요구 사항을 준수할 수 있습니다.
- **여러 소스의 보안 데이터 활용**. 네트워크 방화벽과 같은 연결된 파트너 솔루션 및 기타 Microsoft 서비스를 비롯한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다. 
- **기존 보안 워크플로와 통합**. REST API를 사용해 기존 도구와 프로세스에 연결하여 보안 정보를 평가, 통합 및 분석할 수 있습니다.
- **규정 준수 보고**. 보안 데이터와 정보를 사용하여 규정을 준수함을 제시하고 감사자를 위해 증거를 쉽게 생성할 수 있습니다.

**적응형 위협 방지**

- **지속적인 보안 평가**. 수백 가지 기본 제공 보안 평가를 사용하거나 보안 평가를 직접 만들어 컴퓨터, 네트워크 및 Azure 서비스의 보안을 모니터링할 수 있으며, 공격에 취약한 소프트웨어와 구성을 식별할 수 있습니다.
- **실행 가능한 권장 사항**. 우선 순위가 지정된 실행 가능한 보안 권장 사항과 기본 제공 자동화 Playbook을 사용하여 공격자들이 익스플로잇하기 전에 보안 취약성을 수정할 수 있습니다.
- **적응형 응용 프로그램 제어**. Machine Learning을 통해 제공되며 특정 Azure 작업에 맞게 조정되는 허용 목록 권장 사항을 적용하여 맬웨어 및 기타 원치 않는 응용 프로그램을 차단할 수 있습니다. 
- **네트워크 액세스 보안**. Azure VM의 관리 포트에 대한 제어되는 Just-In-Time 액세스를 사용하여 네트워크 공격에 대한 취약성을 낮춤으로써 무차별 암호 대입 공격(brute force attack) 및 기타 네트워크 공격에 대한 노출을 크게 줄일 수 있습니다.

**지능형 위협 검색 및 대응**

- **업계에서 가장 광범위한 위협 인텔리전스**. 전 세계 Microsoft 서비스와 시스템에서 전송하는 수조 개에 달하는 신호를 사용하여 새로운 위협과 지속적으로 발전하는 위협을 식별하는 Microsoft Intelligent Security Graph를 활용할 수 있습니다.
- **고급 위협 검색**. 기본 제공 행동 분석 및 Machine Learning을 사용하여 공격 및 제로 데이 익스플로잇을 식별할 수 있습니다. 또한 네트워크, 컴퓨터 및 클라우스 서비스에서 들어오는 공격 및 위반 후 활동을 모니터링할 수 있습니다.
- **우선 순위가 지정된 경고 및 인시던트**. 다양한 유형의 경고를 단일 공격 캠페인에 매핑하는 우선 순위가 지정된 보안 경고와 인시던트를 통해 가장 중요한 위협부터 중점적으로 확인할 수 있습니다. 사용자 지정 보안 경고를 만들 수도 있습니다.
- **손쉬운 조사**. 시각적인 대화형 환경을 통해 공격의 범위와 영향을 빠르게 평가할 수 있습니다. 미리 정의된 쿼리나 임시 쿼리를 통해 보안 데이터를 보다 상세하게 탐색할 수 있습니다. 
- **상황별 위협 인텔리전스**. 대화형 세계 지도에 공격의 출처가 표시됩니다. 기본 제공 위협 인텔리전스 보고서를 사용하여 알려진 공격자의 기술 및 목표와 관련된 유용한 정보를 파악할 수 있습니다.

## <a name="get-started"></a>시작
보안 센터를 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 보안 센터는 Azure 구독을 사용하여 사용하도록 설정됩니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다. 

[Azure 보안 센터 시작](https://docs.microsoft.com/azure/security-center/security-center-get-started)은 보안 센터의 보안 모니터링 및 정책 관리 구성 요소를 빠르게 안내합니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터, 주요 기능 및 시작하는 방법을 소개하였습니다. 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md) - 조직의 보안 요구 사항과 클라우드 관리 모델에 따라 Security Center 사용을 최적화하는 방법에 대해 알아봅니다.
* [보안 정책 설정](https://docs.microsoft.com/azure/security-center/security-center-policies) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [보안 권장 사항 관리](https://docs.microsoft.com/azure/security-center/security-center-recommendations) - 권장 사항이 Azure 및 비 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [보안 상태 모니터링](https://docs.microsoft.com/azure/security-center/security-center-monitoring) - Azure 및 비 Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [파트너 솔루션 모니터링](https://docs.microsoft.com/azure/security-center/security-center-partner-solutions) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Security Center FAQ](https://docs.microsoft.com/azure/security-center/security-center-faq) - Security Center 사용에 관한 질문과 대답을 찾습니다.


