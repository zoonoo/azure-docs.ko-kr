---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: a20311a0285bf8fda5498241a60b85093039ad19
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513258"
---
## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 고객은 클러스터 및 클러스터가 빌드된 VM(가상 머신) 확장 세트를 소유합니다. VM 확장 세트에서 Azure Disk Encryption을 설정할 수 있습니다. |
| 전송 중 암호화:<ul><li>Express 경로 암호화</li><li>Vnet 내부 암호화</li><li>VNet 간 암호화</ul>| 예 |  |
| 암호화 키 처리(CMK, BYOK 등)| 예 | 고객은 클러스터 및 클러스터가 빌드된 VM(가상 머신) 확장 세트를 소유합니다. VM 확장 세트에서 Azure Disk Encryption을 설정할 수 있습니다. |
| 열 수준 암호화(Azure Data Services)| 해당 없음 |  |
| API 호출 암호화| 예 | Service Fabric API 호출은 Azure Resource Manager를 통해 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. |

## <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| 예 |  |
| 네트워크 격리 / 방화벽 지원| 예 | NSG(네트워크 보안 그룹) 사용 |
| 강제 터널링에 대한 지원 | 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | Azure 모니터링 지원 및 타사 지원 사용 |

## <a name="iam-support"></a>IAM 지원

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 액세스 관리 - 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 액세스 관리 - 권한 부여| 예 | SFRP를 통한 호출의 IAM(ID 및 액세스 관리). 클러스터 엔드포인트에 대한 직접 호출은 다음 두 역할을 지원합니다. 사용자 및 관리자. 고객은 API를 두 역할 중 하나에 매핑할 수 있습니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사| 예 | 모든 제어 평면 작업은 감사 및 승인 프로세스를 통해 실행합니다. |
| 데이터 평면 로깅 및 감사| 해당 없음 | 고객은 클러스터를 소유합니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | 서비스 구성은 Azure 배포를 사용하여 버전 지정되고 배포됩니다. 코드(애플리케이션 및 런타임)는 Azure 빌드를 사용하여 버전 지정됩니다.
 |