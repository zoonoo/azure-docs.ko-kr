---
title: Azure Security Center에 대 한 릴리스 정보
description: Azure Security Center의 새로운 기능과 변경 된 기능에 대 한 설명입니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210897"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능

Azure 보안은 활성 개발 중 이며 지속적으로 향상 된 기능을 받습니다. 최신 개발을 최신 상태로 유지 하기 위해이 페이지에서는 다음에 대 한 정보를 제공 합니다.

- 새 기능
- 버그 수정
- 사용되지 않는 기능

이 페이지는 정기적으로 업데이트 되므로 자주 다시 방문 하세요. 6 개월 보다 오래 된 항목을 찾는 경우 [Azure Security Center의 새로운 기능에 대 한 보관 파일](release-notes-archive.md)에서 찾을 수 있습니다.


## <a name="may-2020"></a>2020년 5월

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>JIT (just-in-time) VM (가상 컴퓨터) 액세스에 대 한 변경 내용

Security Center에는 Vm의 관리 포트를 보호 하는 선택적 기능이 포함 되어 있습니다. 이는 무차별 암호 대입 공격의 가장 일반적인 형태에 대 한 방어를 제공 합니다.

이 업데이트는이 기능을 다음과 같이 변경 합니다.

- VM에서 JIT를 사용 하도록 설정 하 라는 권장 사항이 변경 되었습니다. 이전에는 "Just-in-time 네트워크 액세스 제어가 가상 컴퓨터에 적용 되어야 합니다." 이제 "just-in-time 네트워크 액세스 제어를 사용 하 여 가상 컴퓨터의 관리 포트를 보호 해야 합니다."

- 권장 구성이 열려 있는 관리 포트가 있는 경우에만 트리거되는 것으로 설정 되었습니다.

[JIT 액세스 기능에 대해 자세히 알아보세요](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>사용자 지정 권장 사항이 별도의 보안 컨트롤로 이동 됨

향상 된 보안 점수를 사용 하 여 도입 된 보안 제어 중 하나는 "보안 모범 사례 구현" 이었습니다. 구독에 대해 생성 된 사용자 지정 권장 사항은 자동으로 해당 컨트롤에 배치 됩니다. 

사용자 지정 권장 사항을 더 쉽게 찾을 수 있도록이를 전용 보안 제어 "사용자 지정 권장 사항"으로 이동 했습니다. 이 컨트롤은 보안 점수에 영향을 주지 않습니다.

[Azure Security Center의 향상 된 보안 점수 (미리 보기)](secure-score-security-controls.md)에서 보안 제어에 대해 자세히 알아보세요.


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>컨트롤의 권장 사항을 보기 위해 추가 하거나 단순 목록으로 설정/해제

보안 제어는 관련 된 보안 권장 사항의 논리적 그룹입니다. 취약 한 공격 표면을 반영 합니다. 컨트롤은 이러한 권장 사항을 구현 하는 데 도움이 되는 지침을 포함 하는 보안 권장 사항 집합입니다.

조직이 각 개별 공격 노출 영역을 얼마나 잘 보호 하 고 있는지 즉시 확인 하려면 각 보안 제어의 점수를 검토 합니다.

기본적으로 권장 사항은 보안 컨트롤에 표시 됩니다. 이 업데이트에서 목록으로 표시할 수도 있습니다. 영향을 받는 리소스의 상태를 기준으로 정렬 된 간단한 목록으로 표시 하려면 새 ' 컨트롤 그룹화 ' 토글을 사용 합니다. 설정/해제는 포털의 목록 위에 있습니다.

보안 제어 및이 토글은 새로운 보안 점수 환경의 일부입니다. 포털 내에서 사용자 의견을 보내 주시기 바랍니다.

[Azure Security Center의 향상 된 보안 점수 (미리 보기)](secure-score-security-controls.md)에서 보안 제어에 대해 자세히 알아보세요.


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>계정 보안 권장 사항이 "보안 모범 사례" 보안 제어로 이동 됨

향상 된 보안 점수를 사용 하 여 도입 된 보안 제어 중 하나는 "보안 모범 사례"입니다. 이 제어에 권장 사항이 있으면 보안 점수에 영향을 주지 않습니다. 

이 업데이트를 사용 하면 세 가지 권장 사항이 원래 배치 된 컨트롤과이 모범 사례 제어로 이동 했습니다. 이 단계는 이러한 세 가지 권장 사항에 대 한 위험이 처음으로 생각 되는 것 보다 낮기 때문에 수행 했습니다.

권장 사항은 다음과 같습니다.

- 구독에 대 한 읽기 권한이 있는 계정에서 MFA를 사용 하도록 설정 해야 합니다 (원래 "MFA 사용" 컨트롤에 있음).
- 읽기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다 (원래 "액세스 및 사용 권한 관리" 제어).
- 구독에 대해 최대 3 개의 소유자를 지정 해야 합니다 (원래 "액세스 및 사용 권한 관리" 제어).

[Azure Security Center의 향상 된 보안 점수 (미리 보기)](secure-score-security-controls.md)에서 보안 제어에 대해 자세히 알아보세요.


### <a name="custom-policies-with-custom-metadata-generally-available"></a>사용자 지정 메타 데이터를 사용 하는 사용자 지정 정책 일반 공급

사용자 지정 정책은 이제 Security Center 권장 사항 환경, 보안 점수 및 규정 준수 표준 대시보드의 일부입니다. 이 기능은 이제 일반 공급 되며, Security Center에서 조직의 보안 평가 적용 범위를 확장할 수 있습니다. 

Azure 정책에서 사용자 지정 이니셔티브를 만들고, 여기에 정책을 추가 하 고 Azure Security Center에 등록 하 고, 권장 사항으로 시각화 합니다.

이제 사용자 지정 권장 구성 메타 데이터를 편집 하는 옵션도 추가 했습니다. 메타 데이터 옵션에는 심각도, 수정 단계, 위협 정보 등이 포함 됩니다.  

자세한 [정보를 사용 하 여 사용자 지정 권장 사항을 강화 하는 방법에 대해 자세히 알아보세요](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


## <a name="april-2020"></a>2020년 4월

### <a name="dynamic-compliance-packages-now-generally-available"></a>동적 준수 패키지가 이제 일반 공급 됩니다.

이제 Azure Security Center 규제 준수 대시보드에는 추가 산업 및 규정 표준을 추적 하는 **동적 준수 패키지** (현재 일반 공급)가 포함 되어 있습니다.

Security Center 보안 정책 페이지에서 구독 또는 관리 그룹에 동적 준수 패키지를 추가할 수 있습니다. 표준 또는 벤치 마크를 등록 면 규정 준수 대시보드에 평가로 매핑된 모든 관련 준수 데이터가 포함 된 표준이 표시 됩니다. 등록 된 표준에 대 한 요약 보고서는 다운로드할 수 있습니다.

이제 다음과 같은 표준을 추가할 수 있습니다.

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **영국 공식 및 영국 NHS**
- **캐나다 연방 PBMM**
- **AZURE cis 1.1.0 (신규)** (azure cis 1.1.0를 보다 완벽 하 게 표현)

또한 최근 준수 프레임 워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대 한 Microsoft에서 작성 한 Azure 관련 지침 인 **Azure 보안 벤치 마크**를 최근 추가 했습니다. 추가 표준은 사용할 수 있게 되 면 대시보드에서 지원 될 예정입니다.  
 
[규정 준수 대시보드의 표준 집합을 사용자 지정 하는 방법에 대해 자세히 알아보세요](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>이제 Azure Security Center 무료 계층에 id 권장 사항이 포함 되어 있습니다.

Azure Security Center 무료 계층에 대 한 id 및 액세스에 대 한 보안 권장 사항이 이제 일반 공급 됩니다. 이는 CSPM (클라우드 보안 상태 관리) 기능을 무료로 제공 하는 노력의 일부입니다. 지금까지 이러한 권장 사항은 표준 가격 책정 계층 에서만 사용할 수 있었습니다.

Id 및 액세스 권장 사항의 예는 다음과 같습니다.

- "구독에 대 한 소유자 권한이 있는 계정에 대해 다단계 인증을 사용 하도록 설정 해야 합니다."
- "구독에 대해 최대 3 개의 소유자를 지정 해야 합니다."
- "구독에서 사용 되지 않는 계정을 제거 해야 합니다."

무료 가격 책정 계층에 대 한 구독이 있는 경우 해당 보안 점수는 id 및 액세스 보안을 평가 하지 않았기 때문에이 변경의 영향을 받습니다.

[Id 및 액세스 권장 사항에 대해 자세히 알아보세요](recommendations-reference.md#recs-identity).
[Id 및 액세스 모니터링에 대해 자세히 알아보세요](security-center-identity-access.md).


## <a name="march-2020"></a>2020년 3월

### <a name="workflow-automation-is-now-generally-available"></a>이제 일반 공급 되는 워크플로 자동화

Azure Security Center의 워크플로 자동화 기능이 이제 일반 공급 됩니다. 이를 사용 하 여 보안 경고 및 권장 사항에 대 한 Logic Apps를 자동으로 트리거합니다. 또한 빠른 수정 옵션을 사용할 수 있는 경고 및 모든 권장 사항에 대해 수동 트리거를 사용할 수 있습니다.

모든 보안 프로그램에는 인시던트 응답을 위한 여러 워크플로가 포함 되어 있습니다. 이러한 프로세스에는 관련 이해 관계자에 게 알리고, 변경 관리 프로세스를 시작 하 고, 특정 수정 단계를 적용할 수 있습니다. 보안 전문가는 가능한 한 해당 절차의 여러 단계를 자동화 하는 것이 좋습니다. Automation은 오버 헤드를 줄이고 프로세스 단계가 미리 정의 된 요구 사항에 따라 빠르고 일관 되 게 수행 되도록 하 여 보안을 향상 시킬 수 있습니다.

워크플로를 실행 하기 위한 자동 및 수동 Security Center 기능에 대 한 자세한 내용은 [워크플로 자동화](workflow-automation.md)를 참조 하세요.

Logic Apps를 만드는 방법에 대 한 자세한 내용은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)을 참조 하세요.


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Windows 관리 센터와 Azure Security Center 통합

이제 Windows 관리 센터에서 Azure Security Center 온-프레미스 Windows server를 바로 이동할 수 있습니다. 그러면 Security Center은 온-프레미스 서버, 가상 컴퓨터 및 추가 PaaS 워크 로드를 포함 하 여 모든 Windows 관리 센터 리소스에 대 한 보안 정보를 볼 수 있는 단일 창이 됩니다.

Windows 관리 센터에서 Azure Security Center로 서버를 이동한 후 다음을 수행할 수 있습니다.

- Windows 관리 센터의 Security Center 확장에서 보안 경고 및 권장 사항을 확인 합니다.
- 보안 상태를 확인 하 고 Azure Portal 또는 API를 통해 Security Center에서 Windows 관리 센터 관리 서버에 대 한 추가 세부 정보를 검색 합니다.

[Windows 관리 센터와 Azure Security Center를 통합](windows-admin-center-integration.md)하는 방법에 대해 자세히 알아보세요.


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대 한 보호

Azure Security Center는 AKS (Azure Kubernetes Service)를 보호 하기 위해 컨테이너 보안 기능을 확장 하 고 있습니다.

널리 사용 되는 오픈 소스 플랫폼 Kubernetes은 이제 컨테이너 오케스트레이션에 대 한 업계 표준으로 널리 채택 되 고 있습니다. 이러한 광범위 한 구현에도 불구 하 고, Kubernetes 환경을 보호 하는 방법에 대 한 이해가 부족 합니다. 컨테이너 화 된 응용 프로그램의 공격 노출 영역을 방어 하려면 인프라가 안전 하 게 구성 되 고 잠재적 위협에 대해 지속적으로 모니터링 되도록 하는 전문 지식이 필요 합니다.

Security Center 방어에는 다음이 포함 됩니다.

- **검색 및 표시 유형** -Security Center에 등록 된 구독 내의 관리 되는 AKS 인스턴스를 지속적으로 검색 합니다.
- **보안 권장** 사항-AKS에 대 한 보안 모범 사례를 준수 하는 데 도움이 되는 조치 가능한 권장 사항입니다. 이러한 권장 사항은 조직의 보안 상태에 포함 되어 표시 되도록 보안 점수에 포함 됩니다. AKS 관련 권장 사항의 예는 "역할 기반 액세스 제어를 사용 하 여 Kubernetes 서비스 클러스터에 대 한 액세스를 제한 해야 합니다."를 참조 하세요.
- **위협 방지** -AKS 배포에 대 한 지속적인 분석 Security Center을 통해 호스트 및 AKS 클러스터 수준에서 검색 된 위협 및 악의적인 활동을 경고 합니다.

[Security Center와 Azure Kubernetes Services 통합에 대해 자세히 알아보세요](azure-kubernetes-service-integration.md).
[Security Center의 컨테이너 보안 기능에 대해 자세히 알아보세요](container-security.md).


### <a name="improved-just-in-time-experience"></a>Just-in-time 환경 개선

관리 포트를 보호 하는 Azure Security Center의 just-in-time 도구의 기능, 작업 및 UI는 다음과 같이 향상 되었습니다. 

- **양쪽 맞춤 필드** -Azure Portal의 just-in-time 페이지를 통해 VM (가상 컴퓨터)에 대 한 액세스를 요청할 때 새 선택적 필드를 사용 하 여 요청에 대 한 근거를 입력할 수 있습니다. 이 필드에 입력 한 정보는 활동 로그에서 추적할 수 있습니다. 
- **중복 된 jit (just-in-time) 규칙 자동 정리** -jit 정책을 업데이트할 때마다 정리 도구가 자동으로 실행 되어 전체 규칙 집합의 유효성을 검사 합니다. 이 도구는 정책의 규칙과 NSG의 규칙 간 불일치를 검색 합니다. 정리 도구에서 불일치를 발견 하면 원인을 확인 하 고이에 대 한 안전한 경우 더 이상 필요 하지 않은 기본 제공 규칙을 제거 합니다. 클리너는 만든 규칙을 삭제 하지 않습니다. 

[JIT 액세스 기능에 대해 자세히 알아보세요](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>사용 되지 않는 웹 응용 프로그램에 대 한 두 가지 보안 권장 사항

웹 응용 프로그램과 관련 된 두 가지 보안 권장 사항은 더 이상 사용 되지 않습니다. 

- IaaS NSGs의 웹 응용 프로그램에 대 한 규칙을 강화 해야 합니다.
    (관련 정책: IaaS의 웹 응용 프로그램에 대 한 NSGs 규칙은 확정 되어야 함)

- App Services에 대 한 액세스를 제한 해야 합니다.
    (관련 정책: App Services에 대 한 액세스를 제한 해야 합니다 [preview]).

이러한 권장 사항은 Security Center 권장 사항 목록에 더 이상 표시 되지 않습니다. 관련 정책은 "Security Center Default" 라는 이니셔티브에 더 이상 포함 되지 않습니다.

[보안 권장 사항에 대해 자세히 알아보세요](recommendations-reference.md).

## <a name="february-2020"></a>2020년 2월

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>Linux에 대 한 Fileless 공격 감지는 현재 미리 보기로 제공 됩니다.

공격자가 stealthier 방법을 사용 하 여 검색을 방지 하는 경우 Windows 뿐만 아니라 Linux의 fileless 공격 감지가 확장 Azure Security Center. Fileless 공격은 소프트웨어 취약성을 악용 하 고, 악성 페이로드를 무해 한 시스템 프로세스에 삽입 하 고, 메모리에서 숨깁니다. 이러한 기술은 다음과 같습니다.

- 디스크에서 맬웨어 추적 최소화 또는 제거
- 디스크 기반 맬웨어 검색 솔루션으로 검색 가능성을 크게 줄임

이 Azure Security Center 위협에 대처 하기 위해 2018 년 10 월에 fileless 공격 감지 기능을 출시 했으며, Linux 에서도 fileless 공격 감지 기능이 확장 되었습니다. 


## <a name="january-2020"></a>2020년 1월

### <a name="enhanced-secure-score"></a>보안 점수 향상

이제 Azure Security Center의 향상 된 버전의 보안 점수 기능이 미리 보기에서 제공 됩니다. 이 버전에서 여러 권장 사항은 취약 한 공격 화면을 더 잘 반영 하는 보안 제어로 그룹화 되어 있습니다 (예: 관리 포트에 대 한 액세스 제한).

미리 보기 단계에서 보안 점수 변경을 숙지 하 고 환경을 보다 안전 하 게 보호 하는 데 도움이 되는 다른 재구성를 결정 합니다.

[Azure Security Center의 향상 된 보안 점수 (미리 보기)](secure-score-security-controls.md)에서 자세히 알아보세요.