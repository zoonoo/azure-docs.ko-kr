---
title: Azure Storage에 대 한 보안 제어
description: Azure Storage 평가를 위한 보안 컨트롤의 검사 목록
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2cc54077456fce1e7e0f47843a762beee8e715f7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526759"
---
# <a name="security-controls-for-azure-storage"></a>Azure Storage에 대 한 보안 제어

이 문서에서는 Azure Storage에 기본 제공 되는 보안 컨트롤을 설명 합니다. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리 키 | yes |  |
| 미사용 서버 쪽 암호화: 고객 관리 키 (BYOK) | yes | [Azure Key Vault에서 고객이 관리 하는 키를 사용 하 저장소 서비스 암호화를](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조 하세요.|
| 열 수준 암호화 (Azure Data Services)| 해당 없음 |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자는 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| API 호출 암호화| yes |  |

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| yes |  |
| VNet 삽입 지원| 해당 없음 |  |
| 네트워크 격리 및 방화벽 지원| yes | |
| 강제 터널링 지원| 해당 없음 |  |

## <a name="monitoring--logging"></a>& 로깅 모니터링

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| yes | Azure Monitor 메트릭|
| 제어 및 관리 평면 로깅 및 감사 | yes | 활동 로그 Azure Resource Manager |
| 데이터 평면 로깅 및 감사| yes | 서비스 진단 로그.|

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | Azure Active Directory, 공유 키, 공유 액세스 토큰입니다. |
| 권한 부여| yes | RBAC, POSIX Acl 및 SAS 토큰을 통한 인증 지원 |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| yes | Azure Resource Manager Api를 통한 리소스 공급자 버전 관리 지원 |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스에서 기본 제공 되는 보안 컨트롤](../../security/fundamentals/security-controls.md)에 대해 자세히 알아보세요.