---
title: Azure Security Center에 예정된 중요한 변경
description: 알아야 하고 계획해야 할 수 있는 Azure Security Center에 예정된 변경입니다
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 05/09/2021
ms.author: memildin
ms.openlocfilehash: ed9ea3abf984f537ab693ccbadb90e2ba091f52d
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109683572"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

| 계획된 변경                                                                                                                                                        | 변경 예상 날짜 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | 2021년 4월                |
| [Kubernetes 경고의 접두사가 "AKS_"에서 "K8s_"로 변경](#prefix-for-kubernetes-alerts-changing-from-aks_-to-k8s_)                                               | 2021년 6월                 |
| [ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | 2021년 6월                 |
| [AWS의 권장 사항은 GA(일반 공급)용으로 릴리스됩니다.](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | 2021년 **8월**           |
| [SQL 데이터 분류 권장 사항 향상](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항

**변경 예상 날짜:** 2021년 4월

다음 두 가지 권장 사항은 더 이상 사용되지 않습니다.

- **클라우드 서비스 역할에 대해 OS 버전을 업데이트해야 함** - 기본적으로 Azure는 Windows Server 2016과 같이 게스트 OS를 서비스 구성(.cscfg)에서 지정한 OS 제품군 내에서 지원되는 최신 이미지로 주기적으로 업데이트합니다.
- **Kubernetes 서비스를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함** - 이 권장 사항의 평가는 원하는 만큼 광범위하지 않습니다. 이 권장 사항의 현재 버전은 궁극적으로 고객의 보안 요구 사항에 맞는 향상된 버전으로 대체됩니다.


### <a name="prefix-for-kubernetes-alerts-changing-from-aks_-to-k8s_"></a>Kubernetes 경고의 접두사가 "AKS_"에서 "K8s_"로 변경

**변경 예상 날짜:** 2021년 6월

Azure Defender for Kubernetes는 최근에 확장되어 온-프레미스 및 다중 클라우드 환경에서 호스트되는 Kubernetes 클러스터를 보호합니다. [Azure Defender for Kubernetes를 사용하여 하이브리드 및 다중 클라우드 Kubernetes 배포 보호(미리 보기)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)에서 자세히 알아봅니다.

Azure Defender for Kubernetes에서 제공하는 보안 경고가 더 이상 Azure Kubernetes Service의 클러스터로 제한되지 않는 사실을 반영하기 위해 경고 유형의 접두사가 "AKS_"에서 "K8s_"로 변경됩니다. 필요한 경우 이름과 설명도 업데이트됩니다. 예를 들면 다음과 같습니다.

|경고(경고 유형)|Description|
|----|----|
|Kubernetes 침투 테스트 도구가 감지됨<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 감사 로그를 분석한 결과, **AKS** 클러스터에서 Kubernetes 침투 테스트 도구의 사용이 감지되었습니다. 이 동작은 합법적일 수 있지만, 공격자가 이러한 공용 도구를 악의적인 목적으로 사용할 수 있습니다.
|||

다음과 같이 변경됩니다.

|경고(경고 유형)|Description|
|----|----|
|Kubernetes 침투 테스트 도구가 감지됨<br>(**K8s** _PenTestToolsKubeHunter)|Kubernetes 감사 로그를 분석한 결과, **Kubernetes** 클러스터에서 Kubernetes 침투 테스트 도구의 사용이 감지되었습니다. 이 동작은 합법적일 수 있지만, 공격자가 이러한 공용 도구를 악의적인 목적으로 사용할 수 있습니다.|
|||

"AKS_"로 시작하는 경고를 참조하는 모든 제거 규칙은 자동으로 변환됩니다. SIEM 내보내기 또는 경고 유형별 Kubernetes 경고를 참조하는 사용자 지정 자동화 스크립트를 설정한 경우 새 경고 유형으로 업데이트해야 합니다.

Kubernetes 경고의 전체 목록은 [Kubernetes 클러스터에 대한 경고](alerts-reference.md#alerts-akscluster)를 참조하세요.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.

ISO 27001의 레거시 구현은 Security Center의 규정 준수 대시보드에서 제거됩니다. Security Center를 사용하여 ISO 27001 규정 준수를 추적하는 경우 모든 관련 관리 그룹 또는 구독에 대한 새 ISO 27001:2013 표준을 온보드하면 현재 레거시 ISO 27001이 대시보드에서 곧 제거됩니다.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001의 레거시 구현을 제거하는 방법에 대한 메시지를 보여주는 Security Center의 규정 준수 대시보드." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>AWS의 권장 사항은 GA(일반 공급)용으로 릴리스됩니다.

**변경 예상 날짜:** 2021년 8월

Azure Security Center는 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 워크로드를 보호합니다.

AWS Security Hub에서 제공되는 권장 사항은 클라우드 커넥터가 도입된 이후 미리 보기로 제공되었습니다. **미리 보기** 로 플래그가 지정된 권장 사항은 보안 점수 계산에 포함되지 않지만 미리 보기 기간이 종료되면 점수를 매길 수 있도록 가능한 한 항상 수정해야 합니다.

이와 같이 변경하면 두 가지 AWS 권장 세트가 GA로 이동합니다.

- [Security Hub의 PCI DSS 컨트롤](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Security Hub의 CIS AWS Foundations Benchmark 컨트롤](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

이것이 GA이고 평가가 AWS 리소스에서 실행되는 경우 결과는 모든 다중 및 하이브리드 클라우드 리소스에 대해 결합된 보안 점수에 영향을 줍니다.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 데이터 분류 권장 사항 향상

**변경 예상 날짜:** Q2 2021

**데이터 분류 적용** 보안 제어에서 **SQL 데이터베이스의 중요 데이터를 분류해야 함** 권장 사항은 Microsoft의 데이터 분류 전략에 맞춰 향상된 새 버전으로 대체됩니다. 그 결과 권장 사항의 ID도 변경됩니다(현재 b0df6f56-862d-4730-8597-38c0fd4ebd59임).



## <a name="next-steps"></a>다음 단계

제품에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.