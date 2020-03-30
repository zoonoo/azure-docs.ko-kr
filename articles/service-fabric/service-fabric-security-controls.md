---
title: Azure 서비스 패브릭에 대한 보안 제어
description: Azure 서비스 패브릭에 대한 보안 제어에 대해 자세히 알아보세요. 기본 제공 보안 컨트롤의 검사 목록을 포함합니다.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645432"
---
# <a name="security-controls-for-azure-service-fabric"></a>Azure 서비스 패브릭에 대한 보안 제어

이 문서에서는 Azure 서비스 패브릭에 기본제공된 보안 컨트롤에 대해 간증권을 간역합니다. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| yes |  |
| VNet 주입 지원| yes |  |
| 네트워크 격리 및 방화벽 지원| yes | NSG(네트워크 보안 그룹) 사용 |
| 강제 터널링 지원| yes | Azure 네트워킹은 강제 터널링을 제공합니다. |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | Azure 모니터링 지원 및 타사 지원 사용 |
| 제어 및 관리 평면 로깅 및 감사| yes | 모든 제어 평면 작업은 감사 및 승인 프로세스를 통해 실행합니다. |
| 데이터 평면 로깅 및 감사| 해당 없음 | 고객은 클러스터를 소유합니다.  |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| yes | SFRP를 통한 호출의 IAM(ID 및 액세스 관리). 클러스터 끝점에 직접 호출하는 것은 사용자와 관리자의 두 가지 역할을 지원합니다. 고객은 API를 두 역할 중 하나에 매핑할 수 있습니다. |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes | 고객은 클러스터와 클러스터가 빌드되는 가상 시스템 규모 집합을 소유합니다. Azure 디스크 암호화는 가상 시스템 규모 집합에서 활성화할 수 있습니다. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | yes | 고객은 클러스터와 클러스터가 빌드되는 가상 시스템 규모 집합을 소유합니다. Azure 디스크 암호화는 가상 시스템 규모 집합에서 활성화할 수 있습니다. |
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes |  |
| API 호출 암호화| yes | Service Fabric API 호출은 Azure Resource Manager를 통해 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.