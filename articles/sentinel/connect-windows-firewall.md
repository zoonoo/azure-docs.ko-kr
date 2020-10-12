---
title: Azure 센티널에 Windows Defender 방화벽 데이터 연결 | Microsoft Docs
description: Log Analytics 에이전트가 설치 된 Windows 컴퓨터에서 방화벽 이벤트를 쉽게 스트리밍하려면 Azure 센티널의 Windows 방화벽 커넥터를 사용 하도록 설정 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 5518da7d22d14de105c07e88b14e94d4b184269b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90883821"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>고급 보안이 포함 된 Windows Defender 방화벽을 Azure 센티널에 연결

[고급 보안이 포함 된 Windows Defender 방화벽](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 커넥터를 사용 하면 Azure 센티널에서 작업 영역에 있는 모든 windows 컴퓨터의 고급 보안 로그를 사용 하 여 Windows defender 방화벽을 쉽게 수집할 수 있습니다. 이 연결을 사용 하면 통합 문서에서 Windows 방화벽 이벤트를 보고 분석 하 고, 사용자 지정 경고를 만들고, 사용자 지정 경고를 통합 하 고, 조직의 네트워크에 대 한 자세한 정보를 제공 하 고, 보안 작업 기능을 향상 시킬 수 있습니다. 

이 솔루션은 Log Analytics 에이전트가 설치 된 Windows 컴퓨터에서 Windows 방화벽 이벤트를 수집 합니다. 

> [!NOTE]
> - 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.
>
> - Azure Security Center의 Azure Defender 경고가 이미 Azure 센티널 작업 영역에 수집 된 경우이 커넥터를 통해 Windows 방화벽 솔루션을 사용 하도록 설정할 필요가 없습니다. 그러나이 기능을 사용 하도록 설정 하면 중복 된 데이터가 발생 하지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 모니터링할 컴퓨터가 연결 된 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- **Azure 센티널** 역할 외에도 해당 작업 영역에서 securityinsights 솔루션에 대 한 **Log Analytics 참가자** 역할을 할당 받아야 합니다. [자세한 정보](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>커넥터 사용 

1. Azure 센티널 포털의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 커넥터 갤러리에서 **Windows 방화벽** 을 선택 하 고 **커넥터 페이지 열기**를 클릭 합니다.

### <a name="instructions-tab"></a>명령 탭

- **Windows 컴퓨터가 Azure에 있는 경우:**

    1. **Azure Windows 가상 머신에서 에이전트 설치를**선택 합니다.

    1. **다운로드 & Azure Windows Virtual machines 용 에이전트 설치 >** 링크를 클릭 합니다.

    1. **Virtual machines** 목록에서 Azure 센티널로 스트리밍할 Windows 컴퓨터를 선택 합니다. Windows Vm만 표시 되도록 OS 열 필터에서 **windows** 를 선택할 수 있습니다.

    1. 해당 VM에 대해 열리는 창에서 **연결**을 클릭 합니다.

    1. **Virtual Machines** 창으로 돌아가서 연결 하려는 다른 vm에 대해 이전 두 단계를 반복 합니다. 완료 되 면 **Windows 방화벽** 창으로 돌아갑니다.

- **Windows 컴퓨터가 Azure VM이 아닌 경우:**

    1. **비 Azure Windows 컴퓨터에 에이전트 설치를**선택 합니다.

    1. 다운로드를 클릭 하 **& Azure가 아닌 Windows 컴퓨터에 대 한 에이전트 설치 >** 링크를 클릭 합니다.

    1. **에이전트 관리** 창에서 필요에 따라 **windows 에이전트 다운로드 (64 비트)** 를 선택 하거나 **windows 에이전트 (32 비트)를 다운로드**합니다.

    1. **작업 영역 ID**, **기본 키**및 **보조 키** 문자열을 텍스트 파일에 복사 합니다. 해당 파일과 다운로드 한 설치 파일을 Windows 컴퓨터에 복사 합니다. 설치 파일을 실행 하 고 메시지가 표시 되 면 설치 하는 동안 텍스트 파일에 ID 및 키 문자열을 입력 합니다.

    1. **Windows 방화벽** 창으로 돌아갑니다.

1. **솔루션 설치**를 클릭 합니다.

### <a name="next-steps-tab"></a>다음 단계 탭

- Windows 방화벽 로그 데이터에 대 한 정보를 얻으려면 windows **방화벽** 데이터 커넥터와 함께 제공 되는 권장 통합 문서 및 쿼리 샘플을 참조 하세요.

- **로그**에서 Windows 방화벽 데이터를 쿼리하려면 쿼리 창에 **windowsfirewall** 을 입력 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
 
Windows 방화벽 로그는 로컬 로그 파일의 용량에 도달 하는 경우에만 Azure 센티널로 전송 되기 때문에 기본 크기인 4096 KB로 유지 하면 수집 대기 시간이 길어질 가능성이 높습니다. 로그 파일 크기를 낮춰 대기 시간을 낮출 수 있습니다. [Windows 방화벽 로그를 구성 하는](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)방법에 대 한 지침을 참조 하세요. 가능한 최소 로그 크기 (1kb)를 정의 하는 동안 수집 대기 시간이 거의 발생 하지 않지만 로컬 컴퓨터의 성능에 부정적인 영향을 줄 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Windows 방화벽을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

