---
title: Azure Virtual Machine Scale Sets에 대 한 보안 제어
description: Azure Virtual Machine Scale Sets 평가를 위한 보안 컨트롤의 검사 목록
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029419"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets에 대 한 보안 제어

이 문서에서는 Azure Virtual Machine Scale Sets에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 예 |  |
| 강제 터널링 지원| 예 | [Azure Resource Manager 배포 모델을 사용 하 여 강제 터널링 구성을](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)참조 하세요. |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | Azure에서 [Linux 가상 머신 모니터링 및 업데이트](../virtual-machines/linux/tutorial-monitor.md) 및 [azure에서 Windows 가상 머신 모니터링 및 업데이트](../virtual-machines/windows/tutorial-monitor.md)를 참조 하세요. |
| 제어와 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사 | 아니요 |  |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 |  |
| 권한 부여| 예 |  |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | 예 | [Virtual Machine Scale Sets Azure Disk Encryption를](disk-encryption-overview.md)참조 하세요. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | Azure Virtual Machines는 [express](../expressroute/index.yml) 경로 및 VNet 암호화를 지원 합니다. [Vm의 전송 중 암호화를](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)참조 하세요. |
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예 | 고객 관리 키는 지원 되는 Azure 암호화 시나리오입니다. 자세한 내용은 [Azure Disk Encryption를](disk-encryption-overview.md) 참조 하세요 Virtual Machine Scale Sets|
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |
| API 호출 암호화| 예 | HTTPS 및 TLS를 통해 |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 |  | 

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.
