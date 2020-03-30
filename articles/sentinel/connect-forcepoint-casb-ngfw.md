---
title: 포스포인트 제품을 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Forcepoint 제품을 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588232"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>포스포인트 제품을 Azure 센티넬에 연결

> [!IMPORTANT]
> Azure Sentinel의 Forcepoint 제품 데이터 커넥터는 현재 공개 미리 보기 상태입니다. 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


이 문서에서는 Forcepoint 제품을 Azure Sentinel에 연결하는 방법을 설명합니다. 

Forcepoint 데이터 커넥터를 사용하면 Forcepoint 클라우드 액세스 보안 브로커 및 Forcepoint 차세대 방화벽 로그를 Azure Sentinel과 연결할 수 있습니다. 이러한 방식으로 사용자 정의 로그를 실시간으로 Azure Sentinel로 자동으로 내보낼 수 있습니다. 커넥터는 Forcepoint 제품에 의해 기록된 사용자 활동에 대한 풍부한 가시성을 제공합니다. 또한 Azure 워크로드 및 기타 피드의 데이터와 추가 상관 관계를 가능하게 하며 Azure Sentinel 내의 통합 문서를 사용하여 모니터링 기능을 향상시킵니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>포스포인트 제품 로그를 시슬로그 에이전트로 전달 

CeF 형식의 Syslog 메시지를 Syslog 에이전트를 통해 Azure 작업 영역으로 전달하도록 Forcepoint 제품을 구성합니다.

1. 다음 설치 가이드에 설명된 대로 Forcepoint 제품을 Azure Sentinel 통합으로 설정합니다.
 - [포스포인트 CASB 통합 가이드](https://frcpnt.com/casb-sentinel)
 - [포스포인트 NGFW 통합 가이드](https://frcpnt.com/ngfw-sentinel)

2. DeviceVendor 이름의 로그 분석에서 관련 스키마를 사용하려면 CommonSecurityLog를 검색하려면 'Forcepoint'가 포함되어 있습니다. 

3. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).



## <a name="next-steps"></a>다음 단계

이 문서에서는 Forcepoint 제품을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.

- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.