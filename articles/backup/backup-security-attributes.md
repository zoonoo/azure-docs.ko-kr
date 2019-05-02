---
title: Azure Backup에 대 한 일반적인 보안 특성
description: Azure Backup을 평가하기 위한 일반적인 보안 특성 검사 목록
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7bc4fd42f64bba09fd20fedf5e06dc30c3b2cc49
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64680808"
---
# <a name="common-security-attributes-for-azure-backup"></a>Azure Backup에 대 한 일반적인 보안 특성

보안은 Azure 서비스의 모든 측면에 통합됩니다. 이 문서에서는 Azure Backup에 기본 제공 되는 일반적인 보안 특성을 설명 합니다. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 아닙니다. | HTTPS를 사용합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. |  |
| 열 수준 암호화 (Azure Data Services)| 아닙니다. |  |
| API 호출 암호화| 예 |  |

## <a name="network-segmentation"></a>네트워크 분할

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. |  |
| VNet 삽입 지원| 아닙니다. |  |
| 네트워크 격리 및 방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |
| 강제 터널링 지원| 아닙니다. |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Log Analytics는 진단 로그를 통해 지원됩니다. 참조 [모니터 Azure Backup Log Analytics를 사용 하 여 워크 로드 보호](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) 자세한 내용은 합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예 | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. 참조 [Azure Backup 복구 지점 관리에 관한 Access Control](/azure/backup/backup-rbac-rs-vault) 자세한 내용은 합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |
| 데이터 평면 로깅 및 감사| 아닙니다. | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예|  |