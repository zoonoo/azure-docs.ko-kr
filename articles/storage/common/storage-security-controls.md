---
title: 보안 컨트롤
titleSuffix: Azure Storage
description: Azure Storage 평가를 위한 보안 제어 검사 목록을 봅니다. 다루는 영역은 데이터 보호, 네트워크, 모니터링 및 로깅, id 및 구성입니다.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: b816e7bd5e00b21700bc994fc0860195d39f2915
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826520"
---
# <a name="security-controls-for-azure-storage"></a>Azure Storage에 대 한 보안 제어

이 문서에서는 Azure Storage에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | 예 |  |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예 | [Azure Key Vault에서 고객이 관리 하는 키를 사용 하 저장소 서비스 암호화를](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조 하세요.|
| 열 수준 암호화(Azure Data Services)| 해당 없음 |  |
| 전송 중 암호화(예: ExpressRoute 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자는 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| API 호출 암호화| 예 |  |

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| 서비스 태그 지원| 예 | Azure Storage에서 지 원하는 서비스 태그에 대 한 자세한 내용은 [Azure 서비스 태그 개요](../../virtual-network/service-tags-overview.md) 를 참조 하세요. |
| VNet 삽입 지원| 해당 없음 |  |
| 네트워크 격리 및 방화벽 지원| 예 | |
| 강제 터널링 지원| 해당 없음 |  |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | Azure Monitor 메트릭|
| 제어와 관리 평면 로깅 및 감사 | 예 | Azure 동작 로그 |
| 데이터 평면 로깅 및 감사| 예 | Azure Monitor 리소스 로그 |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 | Azure Active Directory, 공유 키, 공유 액세스 토큰입니다. |
| 권한 부여| 예 | RBAC, POSIX Acl 및 SAS 토큰을 통한 인증 지원 |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | Azure Resource Manager Api를 통한 리소스 공급자 버전 관리 지원 |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.