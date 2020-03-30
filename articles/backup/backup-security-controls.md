---
title: 보안 컨트롤
description: Azure 백업 서비스에 사용되는 보안 컨트롤에 대해 알아봅니다. 이러한 컨트롤은 서비스가 보안 취약점을 방지, 탐지 및 대응하는 데 도움이 됩니다.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172135"
---
# <a name="security-controls-for-azure-backup"></a>Azure 백업에 대한 보안 제어

이 문서에서는 Azure Backup에 기본 제공된 보안 컨트롤을 문서화합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 | 설명서
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |  |
| VNet 주입 지원| 예 |  |  |
| 네트워크 격리 및 방화벽 지원| yes | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |  |
| 강제 터널링 지원| 예 |  |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모| | 설명서
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | 로그 분석은 리소스 로그를 통해 지원됩니다. 자세한 내용은 [Log Analytics를 사용하여 Azure Backup보호 워크로드 모니터링을](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)참조하세요. |  |
| 제어 및 관리 평면 로깅 및 감사| yes | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |  |
| 데이터 평면 로깅 및 감사| 예 | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |  |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모| | 설명서
|---|---|--|
| 인증| yes | 인증은 Azure Active Directory를 통해 수행됩니다. |  |
| 권한 부여| yes | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. 자세한 내용은 [역할 기반 액세스 제어 를 사용하여 Azure 백업 복구 지점을 관리합니다.](/azure/backup/backup-rbac-rs-vault) |  |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 | | 설명서
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |  |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 |  |  |
| 열 수준 암호화(Azure 데이터 서비스)| 예 |  |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | HTTPS를 사용합니다. |  |
| API 호출 암호화| yes |  |  |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모| | 설명서
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes|  |  |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
