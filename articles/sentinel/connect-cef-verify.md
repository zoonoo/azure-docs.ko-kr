---
title: Azure 센티널에 대 한 연결 유효성 검사 | Microsoft Docs
description: 보안 솔루션 연결의 유효성을 검사 하 여 CEF 메시지가 Azure 센티널로 전달 되는지 확인 합니다.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588436"
---
# <a name="step-3-validate-connectivity"></a>3 단계: 연결 유효성 검사



에이전트를 배포 하 고 CEF 메시지를 전달 하도록 보안 솔루션을 구성한 후이 문서를 사용 하 여 Azure 센티널과 보안 솔루션 간의 연결을 확인 하는 방법을 이해할 수 있습니다. 

## <a name="how-to-validate-connectivity"></a>연결의 유효성을 검사 하는 방법

1. Log Analytics를 열어 CommonSecurityLog 스키마를 사용 하 여 로그를 받았는지 확인 합니다.<br> 로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 

1. 스크립트를 실행 하기 전에 보안 솔루션에서 메시지를 전송 하 여 구성한 Syslog 프록시 컴퓨터로 전달 하는 것이 좋습니다. 
1. 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다. 다음 명령을 사용 하 여 컴퓨터에 Python이 있는지 확인 합니다. `python –version`
1. 다음 스크립트를 실행 하 여 에이전트, Azure 센티널 및 보안 솔루션 간의 연결을 확인 합니다. 디먼 전달이 제대로 구성 되어 있고, 올바른 포트에서 수신 대기 하 고, 디먼과 Log Analytics 에이전트 간의 통신을 차단 하 고 있지 않은지 확인 합니다. 또한이 스크립트는 모의 연결을 확인 하기 위해 모의 메시지 ' TestCommonEventFormat '를 보냅니다. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.

