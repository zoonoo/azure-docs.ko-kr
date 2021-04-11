---
title: Azure Security Center에 예정된 중요한 변경
description: 알아야 하고 계획해야 할 수 있는 Azure Security Center에 예정된 변경입니다
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 6204be2ff52b8aac89b93ac09337b1560255e11d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491885"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

| 계획된 변경                                                                                                                                                        | 변경 예상 날짜 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21가지 보안 제어 간 이동 권장 사항](#21-recommendations-moving-between-security-controls)                                                           | 2021년 4월                |
| [더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | 2021년 4월                |
| [AWS의 권장 사항은 GA(일반 공급)용으로 릴리스됩니다.](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | 2021년 4월                |
| [SQL 데이터 분류 권장 사항 향상](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21가지 보안 제어 간 이동 권장 사항 

**변경 예상 날짜:** 2021년 4월

다음 권장 사항은 다른 보안 제어로 이동하고 있습니다. 보안 제어는 관련 보안 권장 사항의 논리적 그룹이며, 취약한 공격 노출 영역을 반영합니다. 이러한 이동은 이러한 각 권장 사항에서 목표를 충족하는 가장 적절한 제어 상태에 있도록 보장합니다. 

보안 컨트롤 및 해당 권장에서 각 보안 제어에 어떤 권장 사항이 있는지 알아보세요.

|권장 |변경 및 영향  |
|---------|---------|
|SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.<br>SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.<br>SQL 데이터베이스의 취약성을 수정해야 함 신규<br>VM의 SQL 데이터베이스 취약성을 수정해야 함     |취약성 수정(6점 가치)에서<br>보안 구성 수정(4점 가치)으로 이동합니다.<br>사용자 환경에 따라 이러한 권장 사항은 점수에 미치는 영향을 줄일 수 있습니다.|
|구독에 둘 이상의 소유자를 할당해야 합니다.<br>자동화 계정 변수를 암호화해야 합니다.<br>IoT 디바이스 - Auditd 프로세스가 이벤트 전송을 중지함<br>IoT 디바이스 - 운영 체제 기준 유효성 검사 실패<br>IoT 디바이스 - TLS 암호화 도구 모음 업그레이드 필요<br>IoT 디바이스 - 디바이스에서 포트 열기<br>IoT 디바이스 - 체인 중 하나에서 허용되는 방화벽 정책이 발견됨<br>IoT 디바이스 - 체인 중 하나에서 허용되는 방화벽 정책이 발견됨<br>IoT 디바이스 - 출력 체인에서 허용되는 방화벽 규칙이 발견됨<br>IoT Hub의 진단 로그를 사용하도록 설정해야 합니다.<br>IoT 디바이스 - 에이전트에서 미달 사용된 메시지를 보내고 있음<br>IoT 디바이스 - 기본 IP 필터 정책은 거부여야 함<br>IoT 디바이스 - IP 필터 규칙 큰 IP 범위<br>IoT 디바이스 - 에이전트 메시지 간격 및 크기를 조정해야 함<br>IoT 디바이스 - 동일한 인증 자격 증명<br>IoT 디바이스 - Audited 프로세스가 이벤트 전송을 중지함<br>IoT 디바이스 - OS(운영 체제) 기준 구성을 수정해야 함|**보안 모범 사례 구현** 으로 이동합니다.<br>권장 사항이 점수 가치가 없는 보안 모범 사례 구현 보안 제어로 이동하는 경우 해당 권장 사항은 더 이상 보안 점수에 영향을 주지 않습니다.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>더 이상 사용되지 않는 "시스템 업데이트 적용" 보안 제어의 두 가지 권장 사항

**변경 예상 날짜:** 2021년 4월

다음 두 가지 권장 사항은 더 이상 사용되지 않습니다.

- **클라우드 서비스 역할에 대해 OS 버전을 업데이트해야 함** - 기본적으로 Azure는 Windows Server 2016과 같이 게스트 OS를 서비스 구성(.cscfg)에서 지정한 OS 제품군 내에서 지원되는 최신 이미지로 주기적으로 업데이트합니다.
- **Kubernetes 서비스를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함** - 이 권장 사항의 평가는 원하는 만큼 광범위하지 않습니다. 이 권장 사항의 현재 버전은 궁극적으로 고객의 보안 요구 사항에 맞는 향상된 버전으로 대체됩니다.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>AWS의 권장 사항은 GA(일반 공급)용으로 릴리스됩니다.

**변경 예상 날짜:** 2021년 4월

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