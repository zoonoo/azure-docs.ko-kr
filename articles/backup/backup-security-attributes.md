---
title: Azure Backup에 대 한 보안 특성
description: Azure Backup 평가를 위한 보안 특성의 검사 목록
author: utraghuv
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: c2500c6c9ff6882e521f4edce02426a92a0bd39f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464937"
---
# <a name="security-attributes-for-azure-backup"></a>Azure Backup에 대 한 보안 특성

이 문서에서는 Azure Backup에 기본 제공 되는 보안 특성을 설명 합니다. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능)| 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 아니요 | HTTPS를 사용합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 |  |
| 열 수준 암호화 (Azure Data Services)| 아니요 |  |
| API 호출 암호화| 예 |  |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 |  |
| VNet 삽입 지원| 아니요 |  |
| 네트워크 격리 및 방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |
| 강제 터널링 지원| 아니요 |  |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Log Analytics는 진단 로그를 통해 지원됩니다. 자세한 내용은 [Log Analytics를 사용 하 여 Azure Backup 보호 된 워크 로드 모니터링](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) 을 참조 하세요. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| Authorization| 예 | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. 자세한 내용은 [역할 기반 Access Control를 사용 하 여 Azure Backup 복구 지점의 관리를](/azure/backup/backup-rbac-rs-vault) 참조 하세요. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |
| 데이터 평면 로깅 및 감사| 아니요 | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예|  |