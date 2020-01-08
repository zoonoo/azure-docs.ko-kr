---
title: Azure Security Center 계층 가격 책정
description: Azure Security Center는 무료 및 표준의 두 계층으로 제공 됩니다. 이 페이지에서는 무료에서 표준으로 업그레이드 하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: 9979a672e8a149fb384d0142659a19b8227647fa
ms.sourcegitcommit: 541e6139c535d38b9b4d4c5e3bfa7eef02446fdc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75667456"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>보안 강화를 위해 표준 계층으로 업그레이드
Azure Security Center는 Azure, 온-프레미스 및 기타 클라우드용으로 통합 보안 관리 및 고급 위협 보호 기능을 제공합니다. 또한 하이브리드 클라우드 작업을 확인하고 제어하는 기능, 위협에 대한 노출을 줄이는 적극적인 방어 기능, 그리고 빠르게 발전하는 사이버 공격에 대응할 수 있는 지능형 검색 기능을 제공합니다.

## <a name="pricing-tiers"></a>가격 책정 계층
Security Center는 두 계층으로 제공됩니다.

- Azure Portal에서 Azure Security Center 대시보드를 처음 방문 하거나 API를 통해 프로그래밍 방식으로 사용 하도록 설정한 경우 **무료** 계층은 모든 Azure 구독에서 사용 하도록 설정 됩니다. 무료 계층은 Azure 리소스를 보호할 수 있도록 보안 정책, 지속적인 보안 평가 및 실행 가능한 보안 권장 사항을 제공 합니다.
- **표준** 계층의 경우 무료 계층의 기능이 프라이빗 클라우드 및 기타 퍼블릭 클라우드에서 실행되는 작업으로 확장 적용되며, 하이브리드 클라우드 작업 전반에 걸쳐 통합 보안 관리 및 위협 방지 기능이 제공됩니다. 또한 표준 계층은 기본 제공 동작 분석 및 기계 학습을 사용 하 여 공격 및 제로 일 악용을 식별 하는 고급 위협 검색 기능을 추가 하 고, 액세스 및 응용 프로그램 제어를 통해 네트워크 공격 및 맬웨어에 대 한 노출을 줄입니다. 개. 또한 표준 계층은 가상 컴퓨터에 대 한 취약성 검색을 추가 합니다. 표준 계층을 무료로 사용해 볼 수 있습니다. Security Center Standard는 Vm, 가상 머신 확장 집합, App Service, SQL server 및 저장소 계정을 비롯 한 Azure 리소스를 지원 합니다. Azure Security Center Standard를 사용 하는 경우 리소스 유형에 따라 지원 옵트아웃 (opt out) 할 수 있습니다. 

Vm에 대 한 무료 계층 보안 평가의 대부분 표준 계층 보안 경고에는 Microsoft Monitoring Agent (MMA) 기능을 설치 해야 합니다. Security Center에서 자동 프로 비전을 사용 하도록 설정 하 여 Azure Vm에 대 한 에이전트를 자동으로 배포할 수 있습니다.

## <a name="try-standard-free-for-30-days"></a>30일간 표준 계층 사용
표준 계층은 처음 30일간 무료로 제공됩니다. 30일 종료 시 서비스를 계속 사용하기로 선택하는 경우 사용량에 대한 요금이 자동으로 부과되기 시작합니다.

전체 Azure 구독을 구독 내의 모든 리소스에서 상속되는 표준 계층으로 업그레이드할 수 있습니다.

표준 계층 얻기:

1. **Security Center** 주 메뉴에서 **가격 책정 & 설정** 을 선택 합니다.
2. 표준으로 업그레이드하려는 경우 구독을 선택합니다.
3. **가격 책정 계층**을 선택합니다.
4. **표준**을 선택하여 업그레이드합니다.
5. **저장**을 클릭합니다.

[![Security Center 가격 책정](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> 모든 Security Center 기능을 사용하도록 설정하려면 해당하는 가상 머신이 포함된 구독에 표준 가격 책정 계층을 적용해야 합니다. 작업 영역에 대 한 가격 책정 구성은 just-in-time VM 액세스, 적응 응용 프로그램 제어 및 Azure 리소스에 대 한 네트워크 검색을 사용 하도록 설정 하지 않습니다.
>

## <a name="why-upgrade-to-standard"></a>표준으로 업그레이드하는 이유?
Security Center에서는 다음을 비롯하여 하이브리드 클라우드 작업을 위한 강화된 보안 및 위협 방지 기능을 제공합니다.

- **하이브리드 보안** – 모든 온-프레미스 및 클라우드 작업에 걸쳐 보안을 통합 확인할 수 있습니다. 또한 보안 정책을 적용하고 하이브리드 클라우드 작업의 보안을 지속적으로 평가하여 보안 표준을 준수할 수 있습니다. 방화벽 및 기타 파트너 솔루션을 포함 하 여 여러 원본에서 보안 데이터를 수집, 검색 및 분석 합니다.
- **고급 위협 검색** - 고급 분석 및 Microsoft Intelligent Security Graph를 사용하여 갈수록 발전하는 사이버 공격을 효율적으로 대응할 수 있습니다. 기본 제공된 행동 분석 및 머신 러닝을 활용하여 공격 및 제로 데이 익스플로잇을 식별해보세요. 네트워크, 컴퓨터 및 클라우스 서비스에서 들어오는 공격 및 공격 당한 후 상황에 대해 모니터링하세요. 대화형 도구 및 상황에 맞는 위협 인텔리전스로 조사 절차를 간소화할 수 있습니다.
- **가상 컴퓨터에 대 한 취약성 검색** -취약성 관리를 위한 업계의 가장 고급 솔루션을 제공 하는 모든 가상 컴퓨터에 스캐너를 쉽게 배포할 수 있습니다. Security Center 내에서 검색 결과를 직접 확인, 조사 및 재구성 합니다. 
- **액세스 및 응용 프로그램 제어** -특정 워크 로드에 맞게 조정 된 machine learning powered 허용 목록 권장 사항을 적용 하 여 맬웨어 및 기타 원치 않는 응용 프로그램을 차단 합니다. Azure Vm의 관리 포트에 대 한 적시의 제어 된 액세스로 네트워크 공격 노출 영역을 줄입니다. 따라서 무차별 암호 대입 및 기타 네트워크 공격에 대 한 노출을 크게 줄일 수 있습니다.
- **컨테이너 보안 기능** -컨테이너 화 된 환경에서 취약점 관리 및 실시간 위협 감지를 활용 합니다. 컨테이너 레지스트리 리소스를 사용 하도록 설정 하는 경우 모든 기능을 사용 하도록 설정할 때까지 최대 12 시간이 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center의 가격 책정 방식에 대해 알아보았습니다. 표준 계층의 강화된 보안 및 고급 위협 보호에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [고급 위협 감지](security-center-threat-report.md)
- [Just-in-time VM 액세스 제어](security-center-just-in-time.md)
- [컨테이너 보안 개요](container-security.md)
- [선택한 통화 및 해당 지역에 따라 가격 정보](https://azure.microsoft.com/pricing/details/security-center/)