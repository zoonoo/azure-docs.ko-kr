---
author: msmbaldwin
ms.service: backup
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 5a4cc3ea822c5613fc7a50b8e370d6846b683721
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513298"
---
## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |
| 전송 중 암호화:<ul><li>Express 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 아니요 | HTTPS 사용 |
| 암호화 키 처리(CMK, BYOK 등)| 아니요 |  |
| 열 수준 암호화(Azure Data Services)| 아니요 |  |
| API 호출 암호화| 예 |  |

## <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 |  |
| vNET 삽입 지원| 아니요 |  |
| 네트워크 격리 / 방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |
| 강제 터널링에 대한 지원 | 아니요 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | Log Analytics는 진단 로그를 통해 지원됩니다. 자세한 내용은 Log Analytics를 사용하여 Azure Backup 보호 워크로드 모니터링(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)을 참조하세요. |

## <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 액세스 관리 - 권한 부여| 예 | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. 자세한 내용은 역할 기반 액세스 제어를 사용하여 Azure Backup 복구 지점 관리(https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)를 참조하세요. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |
| 데이터 평면 로깅 및 감사| 아니요 | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예|  |