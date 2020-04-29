---
title: Azure Security Center 보안 점수
description: Azure Security Center의 보안 점수 및 보안 컨트롤에 대 한 설명
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415823"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Azure Security Center의 향상 된 보안 점수 (미리 보기)

## <a name="introduction-to-secure-score"></a>보안 점수 소개

Azure Security Center에는 현재 보안 상황을 이해 하 고 효과적이 고 효율적으로 보안을 개선 하는 데 도움이 되는 두 가지 주요 목표가 있습니다. 이러한 목표를 달성 하는 데 사용할 수 있는 Security Center의 중심 측면은 보안 점수입니다.

Security Center는 리소스, 구독 및 조직을 지속적으로 평가 하 여 보안 문제를 해결 합니다. 그런 다음 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계 합니다. 점수가 높을수록 식별 된 위험 수준을 낮춥니다. 점수를 사용 하 여 조직의 보안 노력과 프로젝트를 추적할 수 있습니다. 

Security Center의 보안 점수 페이지에는 다음이 포함 됩니다.

- **점수** -보안 점수는 백분율 값으로 표시 되지만 기본 값도 지워집니다.

    [![기본 숫자를 사용 하 여 백분율 값으로 표시 되는 보안 점수](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **보안 제어** -각 컨트롤은 관련 된 보안 권장 사항의 논리적 그룹이 며 공격에 취약 한 영역을 반영 합니다. 컨트롤은 이러한 권장 사항을 구현 하는 데 도움이 되는 지침을 포함 하는 보안 권장 사항 집합입니다. 점수는 컨트롤 내의 단일 리소스에 대 한 *모든* 권장 사항을 재구성 하는 경우에만 향상 됩니다.

    조직이 각 개별 공격 노출 영역을 얼마나 잘 보호 하 고 있는지 즉시 확인 하려면 각 보안 제어의 점수를 검토 합니다.

    자세한 내용은 아래의 [보안 점수를 계산 하는 방법](secure-score-security-controls.md#how-the-secure-score-is-calculated) 을 참조 하세요. 


>[!TIP]
> 권장 사항 수준에서 이전 버전의 Security Center 제안 사항: 단일 리소스에 대 한 권장 사항을 재구성 하면 보안 점수가 향상 됩니다. 현재는 컨트롤 내의 단일 리소스에 대 한 *모든* 권장 사항을 수정 하는 경우에만 점수가 향상 됩니다. 따라서 리소스의 보안이 향상 된 경우에만 점수가 향상 됩니다.
> 이 향상 된 버전은 아직 미리 보기 상태 이지만 이전 보안 점수 환경은 Azure Portal에서 옵션으로 사용할 수 있습니다. 


## <a name="locating-your-secure-score"></a>보안 점수 찾기

Security Center에서 점수를 매우 두드러지게 표시 합니다. 개요 페이지에 표시 되는 첫 번째 항목입니다. 전용 보안 점수 페이지를 클릭 하면 구독 별로 점수가 세분화 된 것을 볼 수 있습니다. 단일 구독을 클릭 하 여 우선 순위가 지정 된 권장 사항의 세부 목록과 구독의 점수를 수정 하 게 될 수 있는 잠재적 영향을 확인 합니다.

## <a name="how-the-secure-score-is-calculated"></a>보안 점수를 계산 하는 방법 

전반적인 보안 점수에 대 한 각 보안 제어의 기여는 권장 사항 페이지에서 명확 하 게 표시 됩니다.

[![향상 된 보안 점수에 보안 제어 도입](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

보안 제어를 위해 가능한 모든 요소를 가져오려면 모든 리소스가 보안 제어 내의 모든 보안 권장 사항을 준수 해야 합니다. 예를 들어 Security Center에는 관리 포트를 보호 하는 방법에 대 한 여러 권장 사항이 있습니다. 과거에는 다른 사용자가 해결 하지 못한 하 고 보안 점수가 향상 될 수 있는 반면 이러한 관련 된 권장 사항 중 일부를 수정할 수 있습니다. 객관적으로 살펴보면 보안이 모두 해결 될 때까지 보안이 향상 되지 않도록 하는 것이 쉽습니다. 이제 보안 점수와 차이를 확인 하기 위해 모두 수정 해야 합니다.

예를 들어 "시스템 업데이트 적용" 이라는 보안 제어에는 최대 6 개 점이 있습니다 .이 점수는 컨트롤의 잠재적 증가 값에 대 한 도구 설명에서 볼 수 있습니다.

[![보안 제어 "시스템 업데이트 적용"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

위의 스크린샷에서 "시스템 업데이트 적용" 보안 제어는 "2% (1 포인트)"를 표시 합니다. 즉,이 컨트롤의 모든 권장 사항을 재구성 하면 점수가 2% (이 경우 1 포인트) 씩 증가 합니다. 편의상 권장 구성 목록의 "잠재적 증가" 열 값은 정수로 반올림 됩니다. 도구 설명에 정확한 값이 표시 됩니다.

* **최대 점수** -컨트롤 내에서 모든 권장 사항을 완료 하 여 얻을 수 있는 최대 요소 수입니다. 컨트롤의 최대 점수는 해당 컨트롤의 상대적 중요도를 나타냅니다. 가장 먼저 작업할 문제를 심사 하려면 최대 점수 값을 사용 합니다. 
* **잠재적 증가** -컨트롤 내에서 사용할 수 있는 나머지 요소입니다. 이러한 점수를 보안 점수에 추가 하려면 컨트롤의 모든 권장 사항을 수정 합니다. 위의 예제에서 컨트롤에 대해 표시 되는 한 가지 점은 실제로 0.96 포인트입니다.
* **현재 점수** -이 컨트롤의 현재 점수입니다. 각 컨트롤은 전체 점수를 차지 합니다. 이 예제에서 컨트롤은 5.04에 영향을 주는 합계를 가리킵니다. 

### <a name="calculations---understanding-your-score"></a>계산-점수 이해

|메트릭|수식 및 예제|
|-|-|
|**보안 제어의 현재 점수**|<br>![보안 컨트롤의 현재 점수를 계산 하는 수식](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>각 개별 보안 제어는 보안 점수를 달성 합니다. 컨트롤 내의 권장 구성에 의해 영향을 받는 각 리소스는 컨트롤의 현재 점수를 계산 합니다. 각 컨트롤의 현재 점수는 컨트롤 *내의* 리소스 상태를 측정 한 것입니다.<br>![보안 컨트롤의 현재 점수를 계산할 때 사용 되는 값을 보여 주는 도구 설명](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>이 예제에서 최대 점수는 정상 및 비정상 리소스의 합계인 78로 나뉩니다.<br>6/78 = 0.0769<br>정상 리소스 (4)의 수를 곱하여 현재 점수를 반환 합니다.<br>0.0769 * 4 = **0.31**<br><br>|
|**보안 점수**<br>단일 구독|<br>![현재 보안 점수를 계산 하는 수식](media/secure-score-security-controls/secure-score-equation.png)<br><br>![모든 컨트롤이 활성화 된 단일 구독 보안 점수](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>이 예제에는 모든 보안 컨트롤을 사용할 수 있는 단일 구독이 있습니다 (60 지점의 잠재적 최대 점수). 점수에는 가능한 60에서 28 점이 표시 되 고 나머지 32 지점은 보안 컨트롤의 "잠재적 점수 증가" 그림에 반영 됩니다.<br>![컨트롤 목록 및 잠재적 점수 증가](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**보안 점수**<br>여러 구독|<br>모든 구독에서 모든 리소스에 대 한 현재 점수가 추가 되 고 계산은 단일 구독에 대 한 것과 동일 합니다.<br><br>여러 구독을 볼 때 보안 점수는 모든 사용 가능한 정책 내의 모든 리소스를 평가 하 고 각 보안 제어의 최대 점수에 대해 결합 된 영향을 그룹화 합니다.<br>![모든 컨트롤이 활성화 된 여러 구독에 대 한 보안 점수](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>결합 된 점수가 평균이 **아닙니다** . 모든 구독에서 모든 리소스의 상태에 대 한 평가 된 상태입니다.<br>여기서도 권장 사항 페이지로 이동 하 여 사용할 수 있는 가능한 점수를 추가 하면 현재 점수 (24)와 사용 가능한 최대 점수 (60)의 차이가 있다는 것을 알 수 있습니다.|
||||

## <a name="improving-your-secure-score"></a>보안 점수 향상

보안 점수를 향상 시키려면 권장 사항 목록에서 보안 권장 사항을 수정 하세요. 각 리소스에 대해 각 권장 사항을 수동으로 **수정 하거나 빠른 픽스** 를 사용 하 여 수정할 수 있습니다. 옵션 (사용 가능한 경우)을 사용 하 여 권장 구성에 대 한 수정을 리소스 그룹에 빠르게 적용할 수 있습니다. 자세한 내용은 [권장 사항](security-center-remediate-recommendations.md)수정을 참조 하세요.

>[!IMPORTANT]
> 기본 제공 권장 사항만 보안 점수에 영향을 줍니다.

## <a name="security-controls-and-their-recommendations"></a>보안 제어 및 권장 사항

다음 표에서는 Azure Security Center의 보안 컨트롤을 나열 합니다. *모든* 리소스에 대해 컨트롤에 나열 된 *모든* 권장 사항을 재구성 하는 경우 각 컨트롤에 대해 보안 점수에 추가할 수 있는 최대 점수를 확인할 수 있습니다. 

> [!TIP]
> 이 목록을 다르게 필터링 또는 정렬 하려면 Excel에 복사 하 여 붙여 넣습니다.

|보안 제어|최대 보안 점수 점수|권장 사항|
|----------------|:-------------------:|---------------|
|**MFA 사용**|10|-구독에 대 한 소유자 권한이 있는 계정에서 MFA를 사용 하도록 설정 해야 합니다.<br>-구독에 대 한 읽기 권한이 있는 계정에서 MFA를 사용 하도록 설정 해야 합니다.<br>-MFA에 구독에 대 한 쓰기 권한이 있는 계정을 사용 하도록 설정 해야 합니다.|
|**보안 관리 포트**|8|-Just-in-time 네트워크 액세스 제어는 가상 컴퓨터에 적용 되어야 합니다.<br>-가상 컴퓨터는 네트워크 보안 그룹과 연결 되어야 합니다.<br>-가상 머신에서 관리 포트를 닫아야 합니다.|
|**시스템 업데이트 적용**|6|-컴퓨터에서 모니터링 에이전트 상태 문제를 해결 해야 합니다.<br>-가상 머신 확장 집합에 모니터링 에이전트를 설치 해야 합니다.<br>-모니터링 에이전트가 컴퓨터에 설치 되어 있어야 합니다.<br>-클라우드 서비스 역할에 대 한 OS 버전을 업데이트 해야 합니다.<br>-가상 머신 확장 집합의 시스템 업데이트를 설치 해야 합니다.<br>-시스템 업데이트를 컴퓨터에 설치 해야 합니다.<br>-시스템 업데이트를 적용 하려면 컴퓨터를 다시 시작 해야 합니다.<br>-Kubernetes 서비스를 취약 하지 않은 Kubernetes 버전으로 업그레이드 해야 합니다.<br>-가상 컴퓨터에 모니터링 에이전트를 설치 해야 합니다.|
|**취약점 해결**|6|-SQL server에서 고급 데이터 보안을 사용 하도록 설정 해야 합니다.<br>-Azure Container Registry 이미지의 취약성을 재구성 해야 함<br>-SQL 데이터베이스에 대 한 취약성을 재구성 해야 합니다.<br>-취약점 평가 솔루션에서 취약점을 수정 해야 합니다.<br>-SQL 관리 되는 인스턴스에서 취약점 평가를 사용 하도록 설정 해야 합니다.<br>-SQL server에서 취약성 평가를 사용 하도록 설정 해야 합니다.<br>-취약성 평가 솔루션을 가상 머신에 설치 해야 합니다.|
|**미사용 암호화 사용**|4|-가상 컴퓨터에 디스크 암호화를 적용 해야 합니다.<br>-SQL 데이터베이스의 투명한 데이터 암호화를 사용 하도록 설정 해야 합니다.<br>-Automation 계정 변수를 암호화 해야 합니다.<br>-Service Fabric 클러스터에는 ClusterProtectionLevel 속성이 EncryptAndSign로 설정 되어 있어야 합니다.<br>-SQL server TDE 보호기는 사용자 고유의 키로 암호화 되어야 합니다.|
|**전송 중인 데이터 암호화**|4|-API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.<br>-함수 앱는 HTTPS를 통해 액세스할 수 있어야 합니다.<br>-Redis Cache에 대 한 보안 연결만 사용 하도록 설정 해야 합니다.<br>-저장소 계정에 대 한 보안 전송을 사용 하도록 설정 해야 합니다.<br>-웹 응용 프로그램은 HTTPS를 통해서만 액세스할 수 있어야 합니다.|
|**액세스 및 사용 권한 관리**|4|-구독에 대해 최대 3 개의 소유자를 지정 해야 합니다.<br>-사용 되지 않는 계정은 구독에서 제거 해야 함 (미리 보기)<br>-소유자 권한이 있는 사용 되지 않는 계정은 구독 (미리 보기)에서 제거 해야 합니다.<br>-소유자 권한이 있는 외부 계정을 구독에서 제거 해야 합니다 (미리 보기).<br>-읽기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.<br>-쓰기 권한이 있는 외부 계정을 구독에서 제거 해야 함 (미리 보기)<br>-구독에 둘 이상의 소유자가 할당 되어 있어야 합니다.<br>-Kubernetes Services (미리 보기)에서 RBAC (역할 기반 Access Control)를 사용 해야 합니다.<br>-Service Fabric 클러스터는 클라이언트 인증을 위해 Azure Active Directory만 사용 해야 합니다.|
|**보안 구성 수정**|4|-Pod 보안 정책은 Kubernetes Services에서 정의 해야 합니다.<br>-컨테이너 보안 구성의 취약성을 재구성 해야 함<br>-컴퓨터에서 보안 구성의 취약성을 재구성 해야 함<br>-가상 머신 확장 집합에 대 한 보안 구성의 취약성을 재구성 해야 함<br>-가상 컴퓨터에 모니터링 에이전트를 설치 해야 합니다.<br>-모니터링 에이전트가 컴퓨터에 설치 되어 있어야 합니다.<br>-가상 머신 확장 집합에 모니터링 에이전트를 설치 해야 합니다.<br>-컴퓨터에서 모니터링 에이전트 상태 문제를 해결 해야 합니다.|
|**무단 네트워크 액세스 제한**|4|-가상 컴퓨터의 IP 전달을 사용 하지 않도록 설정 해야 합니다.<br>-권한 있는 IP 범위는 Kubernetes Services (미리 보기)에서 정의 해야 합니다.<br>-(사용 되지 않음) App Services에 대 한 액세스 제한 (미리 보기)<br>-(사용 되지 않음) IaaS NSGs의 웹 응용 프로그램에 대 한 규칙 확정<br>-가상 컴퓨터는 네트워크 보안 그룹과 연결 되어야 합니다.<br>-CORS가 모든 리소스에서 API 앱에 액세스 하도록 허용 해서는 안 됩니다.<br>-CORS에서 모든 리소스가 함수 앱에 액세스할 수 있도록 허용 해서는 안 됩니다.<br>-CORS가 모든 리소스에서 웹 응용 프로그램에 액세스 하도록 허용 해서는 안 됩니다.<br>-API 앱에 대해 원격 디버깅을 해제 해야 합니다.<br>-함수 앱에 대해 원격 디버깅을 해제 해야 합니다.<br>-웹 응용 프로그램에 대해 원격 디버깅을 해제 해야 합니다.<br>-인터넷 연결 Vm을 사용 하는 허용 되는 네트워크 보안 그룹에 대 한 액세스를 제한 해야 합니다.<br>-인터넷 연결 가상 컴퓨터에 대 한 네트워크 보안 그룹 규칙을 강화 해야 합니다.|
|**적응 응용 프로그램 제어 적용**|3|-적응형 응용 프로그램 제어는 가상 머신에서 사용 하도록 설정 해야 합니다.<br>-가상 컴퓨터에 모니터링 에이전트를 설치 해야 합니다.<br>-모니터링 에이전트가 컴퓨터에 설치 되어 있어야 합니다.<br>-컴퓨터에서 모니터링 에이전트 상태 문제를 해결 해야 합니다.|
|**데이터 분류 적용**|2|SQL 데이터베이스의 중요 한 데이터는 분류 되어야 합니다 (미리 보기).|
|**DDoS 공격 으로부터 응용 프로그램 보호**|2|-DDoS Protection Standard를 사용 하도록 설정 해야 합니다.|
|**Endpoint protection 사용**|2|-가상 머신 확장 집합에서 Endpoint protection 상태 오류를 재구성 해야 함<br>-컴퓨터에서 Endpoint protection 상태 문제를 해결 해야 합니다.<br>-Endpoint protection 솔루션은 가상 머신 확장 집합에 설치 해야 합니다.<br>-가상 컴퓨터에 endpoint protection 솔루션 설치<br>-컴퓨터에서 모니터링 에이전트 상태 문제를 해결 해야 합니다.<br>-가상 머신 확장 집합에 모니터링 에이전트를 설치 해야 합니다.<br>-모니터링 에이전트가 컴퓨터에 설치 되어 있어야 합니다.<br>-가상 컴퓨터에 모니터링 에이전트를 설치 해야 합니다.<br>-컴퓨터에 endpoint protection 솔루션을 설치 합니다.|
|**감사 및 로깅 사용**|1|-SQL server에 대 한 감사를 사용 하도록 설정 해야 합니다.<br>-App Services의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Azure Data Lake Store의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Azure Stream Analytics의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Batch 계정의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Data Lake Analytics의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-이벤트 허브의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-IoT Hub의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Key Vault의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Logic Apps의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-검색 서비스의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Service Bus의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-Virtual Machine Scale Sets의 진단 로그를 사용 하도록 설정 해야 합니다.<br>-메트릭 경고 규칙은 Batch 계정에 구성 해야 합니다.<br>-SQL 감사 설정에는 중요 한 작업을 캡처하도록 구성 된 작업 그룹이 있어야 합니다.<br>-SQL server는 90 일 보다 큰 감사 보존 일 수로 구성 해야 합니다.|
|**보안 모범 사례 구현**|0|-방화벽 및 가상 네트워크 구성을 사용 하는 저장소 계정에 대 한 액세스를 제한 해야 합니다.<br>-RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙은 Event Hub 네임 스페이스에서 제거 해야 합니다.<br>-SQL server에 대 한 Azure Active Directory 관리자를 프로 비전 해야 합니다.<br>-이벤트 허브 인스턴스의 권한 부여 규칙이 정의 되어 있어야 합니다.<br>-저장소 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.<br>-가상 컴퓨터를 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.<br>-SQL server에 대 한 고급 데이터 보안 설정에는 보안 경고를 받을 수 있는 전자 메일 주소가 있어야 합니다.<br>-관리 되는 인스턴스에서 고급 데이터 보안을 사용 하도록 설정 해야 합니다.<br>-모든 advanced threat protection 유형은 SQL 관리 되는 인스턴스 고급 데이터 보안 설정에서 사용 하도록 설정 해야 합니다.<br>-SQL server 고급 데이터 보안 설정에서 관리자 및 구독 소유자에 대 한 전자 메일 알림을 사용 하도록 설정 해야 합니다.<br>-SQL server 고급 데이터 보안 설정에서 고급 위협 방지 유형을 ' 모두 '로 설정 해야 합니다.<br>-서브넷은 네트워크 보안 그룹과 연결 되어야 합니다.<br>-모든 advanced threat protection 유형은 SQL server 고급 데이터 보안 설정에서 사용 하도록 설정 되어야 합니다.|
||||

## <a name="secure-score-faq"></a>보안 점수 FAQ

### <a name="why-has-my-secure-score-gone-down"></a>보안 점수가 중단 된 이유는 무엇 인가요?
점수가 계산 되는 방식에 대 한 변경 내용을 포함 하는 향상 된 보안 점수 (현재 미리 보기 상태)로 전환 된 Security Center. 이제 요소를 수신 하는 리소스에 대 한 모든 권장 사항을 해결 해야 합니다. 점수가 0-10로 변경 됩니다.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>보안 제어에서 4 가지 권장 사항 중 세 가지를 해결 하는 경우 보안 점수가 변경 되나요?
아니요. 단일 리소스에 대 한 모든 권장 사항을 수정할 때까지 변경 되지 않습니다. 컨트롤의 최대 점수를 얻으려면 모든 리소스에 대해 모든 권장 사항을 수정 해야 합니다.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>보안 점수의 이전 경험을 계속 사용할 수 있나요? 
예. 의 경우 전환 하기 쉽도록 side-by-side로 실행 됩니다. 이전 모델을 한 번에 단계별로 진행 합니다. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>권장 사항이 나에 게 적용 되지 않는 경우 정책에서 사용 하지 않도록 설정 하면 보안 제어를 충족 하 고 보안 점수를 업데이트 하나요?
예. 사용자 환경에 적용할 수 없는 경우 권장 사항을 사용 하지 않도록 설정 하는 것이 좋습니다. 특정 권장 사항을 사용 하지 않도록 설정 하는 방법에 대 한 지침은 [보안 정책 사용 안 함](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)을 참조 하세요.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>보안 제어에서 보안 점수에 대해 0 점을 제공 하면이를 무시 해야 하나요?
경우에 따라 최대 점수가 0 보다 큰 컨트롤이 표시 되지만 영향은 0입니다. 리소스 수정을 위한 증분 점수가 크지 않은 경우 0으로 반올림 됩니다. 이러한 권장 사항은 여전히 보안이 향상 되기 때문에 무시 하지 마십시오. 유일한 예외는 "추가 모범 사례" 컨트롤입니다. 이러한 권장 사항을 수정 하면 점수가 증가 되지 않지만 전반적인 보안이 향상 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수와이에 해당 하는 보안 제어에 대해 설명 했습니다. 관련 자료는 다음 문서를 참조 하세요.

- [권장 사항에 대 한 다양 한 요소에 대해 알아보기](security-center-recommendations.md)
- [권장 사항을 수정 하는 방법 알아보기](security-center-remediate-recommendations.md)