---
title: Azure 센티널에 Illusive Attack Management 시스템 데이터 연결 | Microsoft Docs
description: Illusive Attack Management 시스템 데이터를 Azure 센티널에 연결 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038120"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Illusive Attack Management 시스템을 Azure 센티널에 연결

> [!IMPORTANT]
> Azure 센티널의 Illusive Attack Management 시스템 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Illusive Attack Management 시스템](https://www.illusivenetworks.com/technology/platform/attack-detection-system) 을 Azure 센티널에 연결 하는 방법을 설명 합니다. Illusive Attack Management System data connector를 사용 하면 Illusive의 공격 노출 영역 분석 데이터 및 인시던트 로그를 Azure 센티널과 공유 하 고 조직의 네트워크 공격 노출 위험 (ASM 대시보드)에 대 한 통찰력을 제공 하 고 조직의 네트워크 (광고 대시보드)에서 무단 측면 이동을 추적 하는 전용 대시보드에서이 정보를 볼 수 있습니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Illusive Attack Management 시스템 로그를 Syslog 에이전트로 전달  

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역에 전달 하도록 Attack Management System을 구성 합니다.

1. Illusive 콘솔에 로그온 하 고 설정->보고로 이동 합니다.

1. Syslog 서버를 찾습니다.

1. 다음 정보를 지정합니다.
   - 호스트 이름: Linux Syslog 에이전트 IP 주소 또는 FQDN 호스트 이름
   - 포트: 514
   - 프로토콜: TCP
   - 감사 메시지: 서버에 감사 메시지 보내기

1. Syslog 서버를 추가 하려면 추가를 클릭 합니다.

1. Illusive Attack Management System의 **로그** 에서 관련 스키마를 사용 하려면 **CommonSecurityLog**를 검색 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Illusive Attack Management System을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
