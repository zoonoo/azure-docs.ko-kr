---
title: 보안 컨트롤
description: Azure Resource Manager 서비스를 평가 하기 위한 기본 제공 보안 컨트롤에 대 한 검사 목록입니다.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485625"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure Resource Manager에 대 한 보안 제어

이 문서에서는 Azure Resource Manager에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리 키 | 예 |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | HTTPS/TLS |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | 해당 없음 | Azure Resource Manager는 고객 콘텐츠를 저장 하지 않고 데이터를 제어 합니다. |
| 열 수준 암호화 (Azure Data Services)| 예 | |
| API 호출 암호화| 예 | |

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 아니요 |  |
| 강제 터널링 지원| 아니요 |  |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 아니요 | |
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그는 리소스에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)을 노출 합니다. 작업 [로그 보기를 참조 하 여 리소스에 대 한 작업을 감사](view-activity-logs.md)합니다. |
| 데이터 평면 로깅 및 감사| 해당 없음 | |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 | [Azure Active Directory](/azure/active-directory) 기반.|
| 권한 부여| 예 | |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 |  |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.