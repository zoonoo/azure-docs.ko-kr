---
title: Azure Security Center 규정 준수 대시보드에서 동적 규제 준수 모니터링으로 업데이트 하는 방법 | Microsoft Docs
description: 규정 준수 패키지 업데이트
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 7a017f9485ec847c0a2c045e4c511b6a68db0cb2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889950"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>규정 준수 대시보드의 표준 집합 사용자 지정

Azure Security Center는 리소스의 구성을 업계 표준, 규정 및 벤치 마크의 요구 사항과 지속적으로 비교 합니다. **규정 준수 대시보드** 는 특정 준수 제어 및 요구 사항을 충족 하는 방법에 따라 규정 준수 상태에 대 한 통찰력을 제공 합니다.


## <a name="overview-of-compliance-packages"></a>호환성 패키지 개요

업계 표준, 규정 표준 및 벤치 마크는 Security Center *준수 패키지로*표시 됩니다.  각 패키지는 Azure Policy에 정의 된 이니셔티브입니다. 대시보드에서 평가로 매핑된 준수 데이터를 확인 하려면 **보안 정책** 페이지 내에서 관리 그룹 또는 구독에 규정 준수 패키지를 추가 합니다. ( [보안 정책을 사용 하 여](tutorial-security-policy.md)Azure Policy 및 이니셔티브에 대해 자세히 알아보세요.)

표준 또는 벤치 마크를 선택한 범위에 등록 하는 경우 해당 표준은 규정 준수 대시보드에 평가로 매핑된 모든 관련 준수 데이터가 표시 됩니다. 등록 된 표준에 대 한 요약 보고서를 다운로드할 수도 있습니다.

또한 Microsoft는 규정 표준 자체를 추적 하 고 시간에 따라 일부 패키지에서 자동으로 해당 범위를 개선 합니다. Microsoft에서 이니셔티브 (표준의 더 많은 컨트롤에 매핑되는 새 정책)에 대해 새 콘텐츠를 릴리스할 때 추가 콘텐츠가 대시보드에 자동으로 표시 됩니다.

> [!TIP]
> Microsoft 릴리스 새 콘텐츠는 **AZURE cis 1.1.0 (신규)** (보다 공식적이 고 [Ci Microsoft Azure 기초 벤치 마크 버전 1.1.0](https://www.cisecurity.org/benchmark/azure/)) 이기 때문에 시간이 지남에 따라 향상 된 한 가지 표준입니다. 모든 Security Center 환경에서 기본적으로 구성 되는 Azure CI의 표현인 "Azure CIS 1.1.0"와 함께 대시보드에이를 추가 해야 합니다. 해당 패키지는 정적 규칙 집합을 사용 합니다. 최신 패키지에는 추가 정책이 포함 되며 시간이 지남에 따라 자동으로 업데이트 됩니다. 아래 설명 된 대로 새 동적 패키지로 업데이트 합니다.


## <a name="available-packages"></a>사용 가능한 패키지

NIST SP 800-53 4, SWIFT CSP CSCF-v2020, 영국 공식 및 영국 NHS, 캐나다 연방 PMM 및 Azure CIS 1.1.0 (신규)와 같은 표준을 추가할 수 있습니다. Azure CIS 1.1.0의 더 완벽 한 표현입니다. 

또한 공통 규정 준수 프레임 워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대 한 Microsoft에서 작성 한 Azure 관련 지침 인 **Azure 보안 벤치 마크**를 추가할 수 있습니다. ([Azure 보안 벤치 마크에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

추가 표준은 사용할 수 있게 되 면 대시보드에서 지원 될 예정입니다. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>대시보드에 규정 표준 추가

다음 단계에서는 패키지를 추가 하 여 지원 되는 규제 표준 중 하나로 준수를 모니터링 하는 방법을 설명 합니다.

> [!NOTE]
> 소유자 또는 정책 참여자 인 사용자 에게만 규정 준수 표준을 추가 하는 데 필요한 권한이 있습니다. 

1. Security Center의 사이드바에서 **규제 준수** 를 선택 하 여 규제 준수 대시보드를 엽니다. 여기에서 현재 선택 된 구독에 현재 할당 된 호환성 표준을 확인할 수 있습니다.   

1. 페이지 맨 위에서 **준수 정책 관리**를 선택 합니다. 정책 관리 페이지가 표시 됩니다.

1. 규정 준수 상태를 관리 하려는 구독 또는 관리 그룹을 선택 합니다. 

    > [!TIP]
    > 규정 준수 데이터가 모든 중첩 된 리소스에 대해 집계 되 고 추적 되도록 표준에 적용할 수 있는 가장 높은 범위를 선택 하는 것이 좋습니다. 

1. 조직과 관련 된 표준을 추가 하려면 **더 많은 표준 추가**를 클릭 합니다. 

1. **규정 준수 표준 추가** 페이지에서 사용 가능한 표준에 대 한 패키지를 검색할 수 있습니다. 사용할 수 있는 몇 가지 표준은 다음과 같습니다.

    - **Azure 보안 벤치마크**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO 및 영국 NHS**
    - **캐나다 PMM**
    
    ![Azure Security Center의 규정 준수 대시보드에 규정 패키지 추가](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Security Center의 사이드바에서 **규제** 준수를 다시 선택 하 여 규정 준수 대시보드로 돌아갑니다.
    * 새 표준은 업계 & 규제 표준 목록에 표시 됩니다. 
    * **AZURE cis 1.1.0 (신규)** 를 추가한 경우 azure cis 1.1.0 규정 준수의 원래 *정적* 보기도 함께 유지 됩니다. 나중에 자동으로 제거 될 수 있습니다.

    > [!NOTE]
    > 새로 추가 된 표준이 준수 대시보드에 표시 되는 데 몇 시간 정도 걸릴 수 있습니다.

    [![이전 및 새 Azure CI를 표시 하는 규정 준수 대시보드](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 문서에서는 추가 표준 준수를 모니터링 하는 **규정 준수 패키지를 추가** 하는 방법을 알아보았습니다. 

기타 관련 자료는 다음 문서를 참조 하세요. 

- [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [보안 센터 규정 준수 대시보드](security-center-compliance-dashboard.md)
- [보안 정책 작업](tutorial-security-policy.md)