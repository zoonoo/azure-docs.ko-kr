---
title: Azure Windows 가상 시스템에 대한 보안 제어
description: Azure Windows 가상 컴퓨터 평가를 위한 보안 컨트롤의 검사 목록
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455345"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows 가상 머신에 대한 보안 제어

이 문서에서는 Windows 가상 컴퓨터에 기본기본보안컨트롤을 문서화합니다.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | |
| VNet 주입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 예 |  |
| 강제 터널링 지원| 예 | [Azure 리소스 관리자 배포 모델을 사용하여 강제 터널링 구성을](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)참조하십시오. |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| 예 | [Azure 에서 Windows 가상 컴퓨터를 모니터링하고 업데이트합니다.](tutorial-monitoring.md) |
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사 | 예 |  |

## <a name="identity"></a>ID

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 |  |
| 권한 부여| 예 |  |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | 예 | [Windows VM의 가상 디스크 암호화를](/azure/virtual-machines/windows/disk-encryption-overview)참조하십시오. |
| 전송 중 암호화(예: 익스프레스라우팅 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | Azure 가상 [머신은 ExpressRoute](/azure/expressroute) 및 VNet 암호화를 지원합니다. [VM의 전송 중 암호화를](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)참조하십시오. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | 예 | 고객 관리 키는 지원되는 Azure 암호화 시나리오입니다. [Azure 암호화 개요를](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)참조하십시오.|
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |
| API 호출 암호화| 예 | HTTPS 및 TLS를 통해. |



## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 |  | 

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
