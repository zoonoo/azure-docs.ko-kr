---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: d8e33113ca9f0886a4cef1c8f9acb855b32c2973
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "58116046"
---
## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 |  |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자가 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| 암호화 키 처리(CMK, BYOK 등)| 예 | 참조 [Azure Key Vault에서 고객 관리 키를 사용 하 여 Storage 서비스 암호화](../articles/storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)합니다.|
| 열 수준 암호화(Azure Data Services)| N/A |  |
| API 호출 암호화| 예 |  |

## <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| N/A |  |
| 네트워크 격리/방화벽 지원| 예 | |
| 강제 터널링 지원 | N/A |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | 사용 가능한 azure Monitor 메트릭 시작 미리 보기를 이제 로그 |

## <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | Azure Active Directory: 공유 키, 공유 액세스 토큰. |
| 액세스 관리 - 권한 부여| 예 | RBAC, POSIX Acl 및 SAS 토큰을 통해 권한 부여를 지원 합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사 | 예 | Azure Resource Manager 활동 로그 |
| 데이터 평면 로깅 및 감사| 예 | 서비스 진단 로그 및 Azure Monitor 로깅 시작 미리 보기  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | Azure Resource Manager Api를 통해 리소스 공급자 버전 관리를 지원 합니다. |