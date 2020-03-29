---
title: 보안 컨트롤
description: Azure 리소스 관리자 서비스를 평가하기 위한 기본 제공 보안 컨트롤의 검사 목록입니다.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485625"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure 리소스 관리자에 대한 보안 제어

이 문서에서는 Azure 리소스 관리자에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | HTTPS/TLS. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 해당 없음 | Azure Resource Manager는 고객 콘텐츠를 저장하지 않고 데이터만 제어합니다. |
| 열 수준 암호화(Azure 데이터 서비스)| yes | |
| API 호출 암호화| yes | |

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | |
| VNet 주입 지원| yes | |
| 네트워크 격리 및 방화벽 지원| 예 |  |
| 강제 터널링 지원| 예 |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| 예 | |
| 제어 및 관리 평면 로깅 및 감사| yes | 활동 로그는 리소스에서 수행된 모든 쓰기 작업(PUT, POST, DELETE)을 노출합니다. 리소스에 [대한 작업을 감사하기 위한 활동 로그 보기](view-activity-logs.md)를 참조하십시오. |
| 데이터 평면 로깅 및 감사| 해당 없음 | |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | [Azure Active 디렉터리](/azure/active-directory) 기반입니다.|
| 권한 부여| yes | |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes |  |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.