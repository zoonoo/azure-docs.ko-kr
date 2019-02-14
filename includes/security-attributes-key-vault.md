---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: aba09012bf2e9d2741f598280add8b599a6f6d1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807087"
---
## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 모든 개체가 암호화됩니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| 암호화 키 처리(CMK, BYOK 등)| 예 | 고객이 Key Vault의 모든 키를 제어합니다. HSM(하드웨어 보안 모듈) 지원 키가 지정되면 FIPS Level 2 HSM은 키, 인증서 또는 비밀을 보호합니다. |
| 열 수준 암호화(Azure Data Services)| 해당 없음 |  |
| API 호출 암호화| 예 | HTTPS를 사용합니다. |

## <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | Vnet(Virtual Network) 서비스 엔드포인트를 사용합니다. |
| vNET 삽입 지원| 아니요 |  |
| 네트워크 격리/방화벽 지원| 예 | Vnet 방화벽 규칙을 사용합니다. |
| 강제 터널링 지원 | 아니요 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | Log Analytics를 사용합니다. |

## <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 액세스 관리 - 권한 부여| 예 | Key Vault 액세스 정책을 사용합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |

## <a name="access-controls"></a>액세스 제어

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예 | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예 | Key Vault 액세스 정책 |
