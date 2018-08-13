---
title: Azure Stack 물리적 장치 감사
description: Azure Stack에 대 한 액세스 감사를 물리적 장치를 통합 하는 방법에 알아봅니다.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/01/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 459cdf4e1a70ee02d818dd6abe101e4fc3475b68
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034842"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Azure Stack 데이터 센터 통합-물리적 장치 감사

베이스 보드 관리 컨트롤러 (Bmc) 및 네트워크 스위치와 같은 Azure Stack에서 모든 물리적 장치에서 감사 로그를 내보내고 전체 감사 솔루션에 통합 되어야 합니다. 장치는 다른 Azure Stack OEM 하드웨어 공급 업체 간에 달라 집니다, 공급 업체 통합 감사에 대 한 설명서에 대 한 연락 합니다. 아래 섹션에서는 Azure Stack에 감사를 물리적 장치에 대 한 몇 가지 일반적인 정보를 제공 합니다.  

## <a name="physical-device-access-auditing"></a>물리적 장치 액세스 감사

Azure Stack에서 모든 물리적 장치 TACACS 또는 RADIUS 사용을 지원 합니다. 베이스 보드 관리 컨트롤러 (BMC) 및 네트워크 스위치에 대 한 액세스를 포함 합니다.

Azure Stack 솔루션 RADIUS 또는 기본 제공 TACACS 제공 되지 않습니다. 그러나 솔루션 유효성이 확인 된 시장에서 기존 RADIUS 또는 TACACS 솔루션의 사용 가능한 사용을 지원 하도록 합니다.

만 RADIUS를 위한 MSCHAPv2 유효성을 검사 했습니다. 이 RADIUS를 사용 하 여 가장 안전한 구현을 나타냅니다.
Azure Stack 솔루션에 포함 된 장치에서 TACAS 또는 RADIUS를 사용 하도록 설정 하려면 OEM 하드웨어 공급 업체에 문의 하세요.

## <a name="syslog-forwarding-for-network-devices"></a>네트워크 장치에 대 한 Syslog 전달

Azure Stack에서 모든 물리적 네트워킹 장치 syslog 메시지를 지원합니다. Syslog 서버를 사용 하 여 azure Stack 솔루션을 제공 하지 마십시오. 그러나 장치 검증 된 시장에서 사용할 수 있는 기존 syslog 솔루션에 보내는 메시지를 지원 하도록 합니다.

Syslog 대상 주소는 배포의 경우 수집 되는 선택적 매개 변수 이지만 추가할 수 있습니다 배포 후. 네트워킹 장치에서 전달 된 syslog를 구성 하려면 OEM 하드웨어 공급 업체에 문의 하세요.

## <a name="next-steps"></a>다음 단계

[서비스 정책](azure-stack-servicing-policy.md)
