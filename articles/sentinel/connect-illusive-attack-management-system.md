---
title: Azure Sentinel에 Illusive Attack Management System 데이터 연결 | Microsoft Docs
description: Azure Sentinel에 Illusive Attack Management System 데이터를 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038120"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Azure Sentinel에 Illusive Attack Management System 연결

> [!IMPORTANT]
> Azure Sentinel의 Illusive Attack Management System 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 이 기능은 Service Level Agreement(서비스 수준 약정) 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Sentinel에 [Illusive Attack Management System](https://www.illusivenetworks.com/technology/platform/attack-detection-system)을 연결하는 방법을 설명합니다. Illusive Attack Management System 데이터 커넥터를 사용하면 Illusive의 공격 표면 분석 데이터와 인시던트 로그를 Azure Sentinel과 공유하므로 조직의 공격 표면 위험에 대한 인사이트를 제공하고(ASM 대시보드) 조직 네트워크에서 발생하는 권한이 없는 수평 이동을 추적하는(ADS 대시보드) 전용 대시보드를 통해 해당 정보를 볼 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Syslog 에이전트에 Illusive Attack Management System 로그 전달  

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역에 전달하도록 Attack Management System을 구성합니다.

1. Illusive 콘솔에 로그온하여 Settings(설정)->Reporting(보고)으로 이동합니다.

1. Syslog 서버를 찾습니다.

1. 다음 정보를 지정합니다.
   - 호스트 이름: Linux Syslog 에이전트 IP 주소 또는 FQDN 호스트 이름
   - 포트: 514
   - 프로토콜: TCP
   - 감사 메시지: 서버에 감사 메시지 보내기

1. Syslog 서버를 추가하려면 Add(추가)를 클릭합니다.

1. Illusive Attack Management System **로그** 의 관련 스키마를 사용하려면 **CommonSecurityLog** 를 검색합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에 Illusive Attack Management System을 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
