---
title: Azure 센티널에 Windows 방화벽 데이터 연결 | Microsoft Docs
description: Windows 방화벽 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588062"
---
# <a name="connect-windows-firewall"></a>Windows 방화벽 연결



Windows 방화벽 커넥터를 사용 하면 Azure 센티널 작업 영역에 연결 된 경우 Windows 방화벽 로그를 쉽게 연결할 수 있습니다. 이 연결을 통해 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이렇게 하면 조직의 네트워크에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 이 솔루션은 Log Analytics 에이전트가 설치 된 Windows 컴퓨터에서 Windows 방화벽 이벤트를 수집 합니다. 


> [!NOTE]
> - 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.
> - Azure 센티널 및 Azure Security Center 같은 작업 영역으로 수집 되는 경우이 커넥터를 통해 Windows 방화벽 솔루션을 사용 하도록 설정할 필요가 없습니다. 그래도 사용 하도록 설정 하면 중복 된 데이터가 발생 하지 않습니다. 

## <a name="enable-the-connector"></a>커넥터 사용 

1. Azure 센티널 포털에서 **데이터 커넥터** 를 선택한 다음 **Windows 방화벽** 타일을 클릭 합니다. 
1.  Windows 컴퓨터가 Azure에 있는 경우:
    1. **Azure Windows 가상 머신에서 에이전트 설치를**클릭 합니다.
    1. **Virtual machines** 목록에서 Azure 센티널로 스트리밍할 Windows 컴퓨터를 선택 합니다. Windows VM 인지 확인 합니다.
    1. 해당 VM에 대해 열리는 창에서 **연결**을 클릭 합니다.  
    1. **Windows 방화벽 커넥터** 창에서 **사용** 을 클릭 합니다. 

2. Windows 컴퓨터가 Azure VM이 아닌 경우:
    1. **비 Azure 컴퓨터에서 에이전트 설치를**클릭 합니다.
    1. **직접 에이전트** 창에서 **windows 에이전트 다운로드 (64 비트)** 또는 **windows 에이전트 다운로드 (32 비트)** 를 선택 합니다.
    1. Windows 컴퓨터에 에이전트를 설치 합니다. **작업 영역 ID**, **기본 키**및 **보조 키** 를 복사 하 고 설치 중에 메시지가 표시 되 면 사용 합니다.

4. 스트리밍할 데이터 형식을 선택 합니다.
5. **솔루션 설치**를 클릭 합니다.
6. Windows 방화벽에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityevent**를 검색 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Windows 방화벽을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

