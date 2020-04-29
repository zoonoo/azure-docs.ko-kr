---
title: Azure 센티널에 Forcepoint products 연결 | Microsoft Docs
description: Forcepoint 제품을 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588232"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Azure 센티널에 Forcepoint 제품 연결

> [!IMPORTANT]
> Azure 센티널의 Forcepoint products 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


이 문서에서는 Forcepoint 제품을 Azure 센티널에 연결 하는 방법을 설명 합니다. 

Forcepoint 데이터 커넥터를 사용 하 여 Forcepoint Cloud Access Security Broker 및 Forcepoint 차세대 방화벽 로그를 Azure 센티널과 연결할 수 있습니다. 이러한 방식으로 사용자 정의 로그를 실시간으로 Azure 센티널로 자동으로 내보낼 수 있습니다. 커넥터는 Forcepoint 제품이 기록한 사용자 활동에 대 한 보강 표시를 제공 합니다. 또한 Azure 워크 로드 및 기타 피드의 데이터와의 상관 관계를 강화 하 고 Azure 센티널 내 통합 문서에 대 한 모니터링 기능을 향상 시킵니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forcepoint 제품 로그를 Syslog 에이전트로 전달 

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달 하도록 Forcepoint 제품을 구성 합니다.

1. 다음 설치 가이드에 설명 된 대로 Forcepoint 제품을 Azure 센티널 통합으로 설정 합니다.
 - [Forcepoint CASB 통합 가이드](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW 통합 가이드](https://frcpnt.com/ngfw-sentinel)

2. CommonSecurityLog를 검색 하 여 DeviceVendor 이름에 ' Forcepoint '가 포함 된 Log Analytics에서 관련 스키마를 사용 합니다. 

3. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Forcepoint 제품을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.

- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.