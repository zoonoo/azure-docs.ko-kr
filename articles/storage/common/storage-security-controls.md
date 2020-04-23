---
title: 보안 컨트롤
titleSuffix: Azure Storage
description: Azure 저장소를 평가하기 위한 보안 컨트롤의 검사 목록입니다.
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082359"
---
# <a name="security-controls-for-azure-storage"></a>Azure 저장소에 대한 보안 제어

이 문서에서는 Azure Storage에 기본 제공된 보안 컨트롤을 문서화합니다.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | 예 |  |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 | [Azure 키 자격 증명 모음에서 고객 관리 키를 사용하여 저장소 서비스 암호화를](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하십시오.|
| 열 수준 암호화(Azure 데이터 서비스)| N/A |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 표준 HTTPS/TLS 메커니즘을 지원합니다.  사용자는 서비스로 전송되기 전에 데이터를 암호화할 수도 있습니다. |
| API 호출 암호화| 예 |  |

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| 서비스 태그 지원| 예 | Azure Storage에서 지원하는 서비스 태그에 대한 자세한 내용은 [Azure 서비스 태그 개요를](../../virtual-network/service-tags-overview.md) 참조하세요. |
| VNet 주입 지원| N/A |  |
| 네트워크 격리 및 방화벽 지원| 예 | |
| 강제 터널링 지원| N/A |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| 예 | Azure 모니터 메트릭|
| 제어 및 관리 평면 로깅 및 감사 | 예 | Azure 리소스 관리자 활동 로그 |
| 데이터 평면 로깅 및 감사| 예 | 서비스 진단 로그.|

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | Azure Active Directory, 공유 키, 공유 액세스 토큰. |
| 권한 부여| 예 | RBAC, POSIX ACL 및 SAS 토큰을 통한 지원 권한 부여 |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | Azure 리소스 관리자 API를 통한 지원 리소스 공급자 버전 관리 |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.