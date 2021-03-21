---
title: Azure 센티널에 Cisco Meraki 데이터 연결 | Microsoft Docs
description: Cisco Meraki 커넥터를 사용 하 여 Azure 센티널로 Cisco Meraki 로그를 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Cisco Meraki 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745989"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Cisco Meraki를 Azure 센티널에 연결

> [!IMPORTANT]
> Cisco Meraki 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

이 문서에서는 Cisco Meraki (MX/MS/MR) 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Cisco Meraki data connector를 사용 하면 Cisco Meraki 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Cisco Meraki와 Azure 센티널을 통합 하면 Log Analytics 에이전트가 설치 된 Syslog 서버를 사용 합니다. 또한 Kusto 함수를 기반으로 하는 사용자 지정 작성 로그 파서를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- Cisco Meraki 솔루션은 Syslog를 통해 로그를 내보내도록 구성 되어야 합니다.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Syslog 에이전트를 통해 Azure 센티널에 Cisco Meraki 로그 보내기  

Syslog 에이전트를 통해 Azure 센티널 작업 영역에 Syslog 메시지를 전달 하도록 Cisco Meraki를 구성 합니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **Cisco Meraki (미리 보기)** 커넥터를 선택한 다음 **커넥터 페이지를 엽니다**.

1. **Cisco Meraki** 커넥터 페이지의 지침을 따릅니다.

    1. Linux 용 에이전트 설치 및 등록

        - Azure Linux VM 또는 비 Azure Linux 컴퓨터 (실제 또는 가상)를 선택 합니다.

    1. 수집할 로그 구성

        - **작업 영역 에이전트 구성** 에서 시설 및 심각도를 선택 합니다.

    1. Cisco Meraki 장치 구성 및 연결

        - [다음 지침](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) 에 따라 syslog를 전달 하도록 Cisco Meraki 장치를 구성 합니다. 원격 서버에 대해 Linux 에이전트를 설치한 Linux 컴퓨터의 IP 주소를 사용 합니다.

## <a name="validate-connectivity-and-find-your-data"></a>연결 유효성 검사 및 데이터 찾기

로그가 Azure 센티널에 나타나기 시작할 때까지 최대 20 분이 걸릴 수 있습니다. 

연결이 설정 되 면 데이터는 로그의 *로그 관리* 섹션 아래 **에 있는** *Syslog* 테이블에 표시 됩니다.

이 데이터 커넥터는 정상적으로 작동 하기 위해 Kusto 함수를 기반으로 하는 파서에 종속 됩니다. [다음 단계를 수행](https://aka.ms/sentinel-ciscomeraki-parser) 하 여 **CiscoMeraki** kusto 함수 별칭을 만듭니다. 그런 다음 `CiscoMeraki` 쿼리 창에 입력 하 여 데이터를 쿼리할 수 있습니다.

몇 가지 유용한 쿼리 샘플은 커넥터 페이지의 **다음 단계** 탭을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Cisco Meraki를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
