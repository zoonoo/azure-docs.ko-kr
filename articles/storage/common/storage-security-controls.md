---
title: Azure 저장소에 대한 보안 제어
description: Azure 저장소 를 평가하기 위한 보안 컨트롤검사 목록
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061121"
---
# <a name="security-controls-for-azure-storage"></a>Azure 저장소에 대한 보안 제어

이 문서에서는 Azure Storage에 기본 제공된 보안 컨트롤을 문서화합니다. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes |  |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | yes | [Azure 키 자격 증명 모음에서 고객 관리 키를 사용하여 저장소 서비스 암호화를](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하십시오.|
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 |  |
| 전송 중 암호화(예: 익스프레스루트 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | 표준 HTTPS/TLS 메커니즘을 지원합니다.  사용자는 서비스로 전송되기 전에 데이터를 암호화할 수도 있습니다. |
| API 호출 암호화| yes |  |

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| yes |  |
| VNet 주입 지원| 해당 없음 |  |
| 네트워크 격리 및 방화벽 지원| yes | |
| 강제 터널링 지원| 해당 없음 |  |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | Azure 모니터 메트릭|
| 제어 및 관리 평면 로깅 및 감사 | yes | Azure 리소스 관리자 활동 로그 |
| 데이터 평면 로깅 및 감사| yes | 서비스 진단 로그.|

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | Azure Active Directory, 공유 키, 공유 액세스 토큰. |
| 권한 부여| yes | RBAC, POSIX ACL 및 SAS 토큰을 통한 지원 권한 부여 |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | Azure 리소스 관리자 API를 통한 지원 리소스 공급자 버전 관리 |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.