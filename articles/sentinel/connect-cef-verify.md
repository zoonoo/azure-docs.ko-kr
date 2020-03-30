---
title: Azure 센티넬에 대한 연결 유효성 검사 | 마이크로 소프트 문서
description: 보안 솔루션의 연결을 확인하여 CEF 메시지가 Azure Sentinel로 전달되는지 확인합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588436"
---
# <a name="step-3-validate-connectivity"></a>3단계: 연결 의 유효성 검사



에이전트를 배포하고 CEF 메시지를 전달하도록 보안 솔루션을 구성한 후 이 문서를 사용하여 Azure Sentinel과 보안 솔루션 간의 연결을 확인하는 방법을 이해합니다. 

## <a name="how-to-validate-connectivity"></a>연결의 유효성을 검사하는 방법

1. 로그 분석을 열어 CommonSecurityLog 스키마를 사용하여 로그가 수신되었는지 확인합니다.<br> 로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다. 

1. 스크립트를 실행하기 전에 보안 솔루션에서 메시지를 보내 구성한 Syslog 프록시 컴퓨터로 전달되는 것이 좋습니다. 
1. 컴퓨터에 높은 사용 권한(sudo)이 있어야 합니다. 다음 명령을 사용하여 컴퓨터에 파이썬이 있는지 확인하십시오.`python –version`
1. 다음 스크립트를 실행하여 에이전트, Azure Sentinel 및 보안 솔루션 간의 연결을 확인합니다. 데몬 포워딩이 제대로 구성되고 올바른 포트에서 수신 이체되며, 데몬과 로그 분석 에이전트 간의 통신을 차단하는 것이 없는지 확인합니다. 스크립트는 또한 종단 간 연결을 확인하기 위해 모의 메시지 'TestCommonEventFormat'을 보냅니다. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

