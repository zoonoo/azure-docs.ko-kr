---
title: Windows 방화벽 데이터를 Azure Sentinel에 연결 | 마이크로 소프트 문서
description: Windows 방화벽 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588062"
---
# <a name="connect-windows-firewall"></a>Windows 방화벽 연결



Windows 방화벽 커넥터를 사용하면 Azure Sentinel 작업 영역에 연결된 경우 Windows 방화벽 로그를 쉽게 연결할 수 있습니다. 이 연결을 사용하면 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 더 많은 통찰력을 얻고 보안 운영 기능을 향상시킬 수 있습니다. 이 솔루션은 Log Analytics 에이전트가 설치된 Windows 컴퓨터에서 Windows 방화벽 이벤트를 수집합니다. 


> [!NOTE]
> - 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.
> - Azure Sentinel 및 Azure 보안 센터가 동일한 작업 영역에 수집되는 경우 이 커넥터를 통해 Windows 방화벽 솔루션을 활성화할 필요가 없습니다. 어쨌든 활성화한 경우 중복된 데이터가 발생하지 않습니다. 

## <a name="enable-the-connector"></a>커넥터 사용 

1. Azure Sentinel 포털에서 데이터 커넥터를 선택한 다음 **Windows 방화벽** 타일을 **클릭합니다.** 
1.  Windows 컴퓨터가 Azure에 있는 경우:
    1. **Azure Windows 가상 컴퓨터에서 에이전트 설치를 클릭합니다.**
    1. 가상 **컴퓨터** 목록에서 Azure Sentinel로 스트리밍할 Windows 컴퓨터를 선택합니다. 이것이 Windows VM인지 확인합니다.
    1. 해당 VM에 대해 열리는 창에서 **연결을**클릭합니다.  
    1. Windows 방화벽 **커넥터** 창에서 **활성화를** 클릭합니다. 

2. Windows 컴퓨터가 Azure VM이 아닌 경우:
    1. **Azure 가 아닌 컴퓨터에서 에이전트 설치를 클릭합니다.**
    1. 직접 **에이전트** 창에서 **Windows 에이전트 다운로드(64비트)** 또는 **Windows 에이전트 다운로드(32비트)를**선택합니다.
    1. Windows 컴퓨터에 에이전트를 설치합니다. 작업 **영역 ID,** **기본 키**및 **보조 키를** 복사하고 설치 하는 동안 메시지가 표시 될 때 사용 합니다.

4. 스트리밍할 데이터 유형을 선택합니다.
5. **솔루션 설치를 클릭합니다.**
6. Windows 방화벽에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityEvent**를 검색합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Windows 방화벽을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

