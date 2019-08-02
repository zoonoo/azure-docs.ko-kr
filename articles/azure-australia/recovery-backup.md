---
title: Azure 오스트레일리아의 백업 및 재해 복구
description: 오스트레일리아 정부 기관에 대 한 Microsoft Azure의 백업 및 재해 복구는 ASD 필수 8과 관련 되어 있습니다.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571524"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Azure 오스트레일리아의 백업 및 재해 복구

지원 인프라를 사용 하 여 백업 및 재해 복구 계획을 구축 하는 것은 모든 조직에서 중요 합니다. [오스트레일리아 사이버 Security Center의 필수 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)에 포함 된 백업 솔루션의 중요성이 강조 표시 됩니다.

Microsoft Azure는 복원 력을 지 원하는 두 가지 서비스를 제공 합니다. Azure Backup 및 Azure Site Recovery. 이러한 서비스를 사용 하면 다양 한 디자인 시나리오에 대해 온-프레미스와 클라우드에서 데이터를 보호할 수 있습니다. Azure Backup 및 Azure Site Recovery는 모두 공통 저장소 및 관리 리소스를 사용 합니다. Azure Recovery Services 자격 증명 모음. 이 자격 증명 모음은 Azure Backup 및 Azure Site Recovery 데이터를 관리, 모니터링 및 분리 하는 데 사용 됩니다.

이 문서에서는 Azure Backup을 구현 하기 위한 주요 디자인 요소를 자세히 설명 하 고 [오스트레일리아 신호 (ASD) ISM (Information Security Manual) 컨트롤](https://acsc.gov.au/infosec/ism/index.htm)을 사용 하 여 Azure Site Recovery 합니다.

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup는 기존의 온-프레미스 백업 솔루션과 비슷하며 온-프레미스와 Azure에서 호스트 되는 데이터를 백업 하는 기능을 제공 합니다. Azure Backup를 사용 하 여 Azure에 다음 데이터 형식을 백업할 수 있습니다.

* 파일 및 폴더
* 에서 호스트 되는 지원 되는 Windows 및 Linux 운영 체제:
  * Hyper-v 및 VMWare 하이퍼바이저
  * 실제 하드웨어
* 지원 되는 Microsoft 응용 프로그램

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery 단일 가상 컴퓨터 또는 다중 계층 응용 프로그램으로 구성 된 작업을 복제 합니다. 복제는 온-프레미스에서 Azure로, Azure 지역 간에 또는 Azure Site Recovery 오케스트레이션 하 여 온-프레미스 위치 간에 지원 됩니다. 온-프레미스 가상 머신은 Azure 또는 지원 되는 온-프레미스 하이퍼바이저로 복제할 수 있습니다. 구성 된 후에는 오케스트레이션 복제, 장애 조치 (failover) 및 장애 복구 (failback)를 Azure Site Recovery 합니다.

## <a name="key-design-considerations"></a>주요 디자인 고려 사항

백업 또는 재해 복구 솔루션을 구현 하는 경우 제안 된 솔루션을 고려해 야 합니다.

* 캡처할 데이터의 범위와 볼륨
* 데이터가 유지 되는 기간
* 이 데이터를 안전 하 게 저장 하 고 관리 하는 방법
* 데이터가 저장 되는 지리적 위치입니다.
* 일상적인 시스템 테스트 절차

ISM은 솔루션을 설계할 때 고려해 야 하는 보안 고려 사항에 대 한 지침을 제공 합니다. Microsoft Azure은 이러한 보안 고려 사항을 해결 하는 수단을 제공 합니다.

### <a name="data-sovereignty"></a>데이터 주권

조직에서는 클라우드 기반 저장소 위치를 utilising 때 데이터 주권 유지 되도록 해야 합니다. Azure Policy은 Azure 리소스를 만들 수 있는 허용 되는 위치를 제한 하는 수단을 제공 합니다. 기본 제공 Azure Policy "허용 된 위치"는 지정 된 Azure Policy의 범위에서 만든 모든 Azure 리소스를 지정 된 지리적 위치 에서만 만들 수 있도록 하는 데 사용 됩니다.

Azure 리소스에 대 한 지리적 제한의 Azure Policy 항목은 다음과 같습니다.

* allowedLocations
* allowedSingleLocation

이러한 정책을 사용 하 여 Azure 관리자는 생성을 특정 위치 목록 또는 단일 지리적 위치로 제한할 수 있습니다.

### <a name="redundant-and-geographically-dispersed-storage"></a>중복 및 지리적으로 분산 된 저장소

Azure 복구 서비스 자격 증명 모음에 저장 된 데이터는 항상 중복 저장소에 저장 됩니다. 기본적으로 Recovery Services 자격 증명 모음은 Azure GRS (지리 중복 저장소)를 사용 합니다. GRS를 사용 하 여 저장 된 데이터는 Recovery Services 자격 증명 모음의 [보조 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)으로 연결 된 지역에서 다른 Azure 데이터 centres에 복제 됩니다. 이 복제 된 데이터는 읽기 전용으로 저장 되며 Azure 장애 조치 (failover) 이벤트가 있는 경우에만 쓰기 가능 하도록 설정 됩니다. Azure 데이터 센터 내에서 별도의 장애 도메인 및 업그레이드 도메인 간에 데이터가 복제 되어 하드웨어 또는 유지 관리 기반 중단 가능성이 최소화 됩니다. GRS는 매년 99.99999999999999%의 가용성을 제공 합니다.

LRS (로컬 중복 저장소)를 제품 Azure Recovery Services 자격 증명 모음을 구성할 수 있습니다. LRS는 가용성 감소를 절충 하는 저렴 한 저장소 옵션입니다. 이 중복성 모델은 별도의 장애 도메인과 업그레이드 도메인 간의 동일한 복제를 채택 하지만 지리적 지역 간에 복제 되지 않습니다. LRS 저장소에 있는 데이터는 GRS 만큼 복원 력이 아니라 매년 99.999999999%의 가용성을 계속 제공 합니다.

테이프 미디어와 같은 기존 오프 사이트 저장소 기술과 달리 데이터의 추가 복사본은 자동으로 생성 되며 추가 관리 오버 헤드가 필요 하지 않습니다.

### <a name="restricted-access-and-activity-monitoring"></a>제한 된 액세스 및 작업 모니터링

백업 데이터는 손상, 수정 및 승인 되지 않은 삭제 로부터 보호 되어야 합니다. Azure Backup와 Azure Site Recovery 모두 공통 Azure 관리 패브릭을 사용 합니다. 이 패브릭을 사용 하면 자세한 감사, 로깅 및 Azure 내에 있는 리소스에 대 한 RBAC (역할 기반 Access Control)를 제공 합니다. 관리 담당자를 선택 하도록 백업 데이터에 대 한 액세스를 제한할 수 있으며, 백업 데이터와 관련 된 모든 작업을 기록 하 고 감사할 수 있습니다.

Azure Backup와 Azure Site Recovery 모두 기본 제공 로깅 및 보고 기능을 제공 합니다. 백업 또는 복제 중에 발생 하는 모든 문제는 Azure 관리 패브릭을 사용 하 여 관리자에 게 보고 됩니다.

Azure Recovery Services 자격 증명 모음에는 다음과 같은 추가 데이터 보안 조치도 있습니다.

* 백업 데이터는 삭제 작업이 발생 한 후 14 일 동안 보존 됩니다.
* "데이터를 삭제 하는 백업 중지"와 같은 중요 한 작업에 대 한 경고 및 알림
* 중요 한 작업에 대 한 보안 PIN 요구 사항
* 최소 보존 범위 검사가 적용 됩니다.

이러한 최소 보존 범위 확인은 다음과 같습니다.

* 일일 보존의 경우 최소 7 일의 보존
* 주간 보존의 경우 최소 4 주 보존
* 월간 보존의 경우 최소 3 개월 보존
* 연간 보존의 경우 최소 1 년의 보존입니다.

Azure에 저장 된 모든 백업 데이터는 Azure의 SSE (저장소 서비스 암호화)를 사용 하 여 미사용 암호화 됩니다. 이는 기본적으로 모든 새 및 기존 저장소 계정에 사용할 수 있으며 사용 하지 않도록 설정할 수 없습니다. 암호화 된 데이터는 검색 중에 자동으로 암호 해독 됩니다. 기본적으로 SSE를 사용 하 여 암호화 된 데이터는 Microsoft에서 제공 하는 키를 사용 하 여 암호화 됩니다. 조직에서는 SSE에서 사용할 자체 암호화 키를 제공 하 고 관리 하도록 선택할 수 있습니다. 암호화 된 데이터에 대 한 선택적 추가 보안 계층을 제공 합니다. 이 키는 고객이 온-프레미스로 또는 Azure 주요 자격 증명 모음 내에서 안전 하 게 저장할 수 있습니다.

### <a name="secure-data-transport"></a>데이터 전송 보안

AES 256를 사용 하 여 전송 중에 암호화 된 데이터를 Azure Backup 합니다. 이 데이터는 백업이 처음 구성 될 때 관리 담당자가 만든 암호를 사용 하 여 보호 됩니다. Microsoft는이 암호에 대 한 액세스 권한이 없습니다. 즉, 고객이이 암호를 안전 하 게 저장 하도록 해야 합니다. 그런 다음 데이터 전송은 보안 HTTPS 연결을 통해 온-프레미스 환경과 Azure Recovery Services 자격 증명 모음 사이에서 발생 합니다.  그러면 Recovery Services 자격 증명 모음 내의 데이터가 Azure SSE를 사용 하 여 미사용 상태로 암호화 됩니다.

Azure Site Recovery 데이터는 전송 중에도 항상 암호화 됩니다. 모든 복제 된 데이터는 HTTPS 및 TLS를 사용 하 여 Azure로 안전 하 게 전송 됩니다. Azure 고객이 Express 경로 연결을 사용 하 여 Azure에 연결 하는 경우이 개인 연결을 통해 Azure Site Recovery 데이터가 전송 됩니다.  Azure 고객이 VPN 연결을 사용 하 여 Azure에 연결 하는 경우 데이터는 인터넷을 통해 온-프레미스와 Recovery Services 자격 증명 모음 간에 안전 하 게 복제 됩니다.

이 보안 네트워크 데이터 전송은 테이프 미디어와 같은 기존 오프 사이트 백업 저장소 솔루션을 관리 하기 위한 보안 위험 및 완화 요구 사항을 제거 합니다.

### <a name="data-retention-periods"></a>데이터 보존 기간

최소 백업 보존 기간으로 3 개월을 권장 하지만 일반적으로 더 긴 보존 기간이 필요 합니다. Azure Backup은 최대 9999 개의 백업 복사본을 제공할 수 있습니다. 보호 된 인스턴스의 단일 Azure Backup 매일 수행 되는 경우 27 년의 매일 백업을 보존할 수 있습니다. 보호 된 인스턴스의 개별 월별 백업은 833 년의 보존을 허용 합니다. 백업 데이터가 오래 되 고 세분화 된 백업 시간이 지남에 따라 유지 되 면 백업 데이터의 총 보존 기간이 증가 합니다.  Azure는 Azure Recovery Services 자격 증명 모음에 데이터를 유지할 수 있는 시간을 제한 하지 않습니다. 인스턴스당 총 백업 수만 있습니다. 이전 백업 또는 새 백업에서 복원 하는 것 사이에는 성능 차이가 없으며, 각 복원은 동일한 시간이 소요 됩니다.

Azure Recovery Services 자격 증명 모음에는 다양 한 기본 백업 및 보존 정책이 있습니다.  관리 직원은 사용자 지정 백업 및 보존 정책을 만들 수도 있습니다.

![Azure Backup 정책](media/create-policy.png)

Azure Backup 및 보존 정책을 구성할 때 백업 빈도와 장기 보존 요구 사항 간의 균형을 확인 해야 합니다.

### <a name="backup-and-restore-testing"></a>백업 및 복원 테스트

복원 또는 장애 조치 (failover)가 필요한 경우 ISM은 백업 데이터를 테스트 하 여 보호 된 데이터가 유효한 지 확인 하는 것이 좋습니다. 또한 Azure Backup 및 Azure Site Recovery은 백업 되거나 복제 된 후에 보호 된 데이터를 테스트 하는 기능을 제공 합니다. Azure Backup에서 관리 하는 데이터는 지정한 위치로 복원할 수 있으며 데이터의 일관성을 확인할 수 있습니다.

Azure Site Recovery는 장애 조치 (failover) 테스트를 수행 하는 기능을 제공 합니다. Recovery Services 자격 증명 모음에 복제 된 워크 로드를 지정한 Azure 환경으로 복원할 수 있습니다. 테스트를 수행 하는 동안 프로덕션 시스템에 영향을 주지 않도록 대상 복원 환경을 프로덕션 환경에서 완전히 격리할 수 있습니다. 테스트가 완료 되 면 테스트 환경 및 모든 리소스를 즉시 삭제 하 여 운영 비용을 절감할 수 있습니다.

Azure 플랫폼에 기본 제공 되는 Azure Automation 서비스를 사용 하 여 장애 조치 (Failover) 테스트 및 유효성 검사를 자동화할 수 있습니다. 이렇게 하면 데이터가 Azure에 성공적으로 복제 되 고 있는지 확인 하기 위해 장애 조치 (failover) 테스트가 자동으로 실행 되도록 예약할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Policy에 대 한 [보안 보장](azure-policy.md)문서를 검토 합니다.
