---
title: 에이전트를 배포하여 CEF 데이터를 Azure Sentinel 미리 보기에 연결합니다| 마이크로 소프트 문서
description: CEF 데이터를 Azure Sentinel에 연결하기 위해 에이전트를 배포하는 방법을 알아봅니다.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588470"
---
# <a name="step-1-deploy-the-agent"></a>1단계: 에이전트 배포


이 단계에서는 Azure Sentinel과 보안 솔루션 간의 프록시 역할을 하는 Linux 컴퓨터를 선택해야 합니다. 다음과 같은 프록시 컴퓨터에서 스크립트를 실행해야 합니다.
- Log 분석 에이전트를 설치하고 필요에 따라 Syslog 메시지를 수신하도록 구성합니다.
- Syslog 데몬을 구성하여 TCP 포트 514를 사용하여 Syslog 메시지를 수신한 다음 TCP 포트 25226을 사용하여 CEF 메시지만 로그 분석 에이전트로 전달합니다.
- Syslog 에이전트가 데이터를 수집하고 Azure Sentinel에 안전하게 전송하도록 설정하여 구문 분석및 보강됩니다.
 
## <a name="deploy-the-agent"></a>에이전트 배포
 
1. Azure Sentinel 포털에서 **데이터 커넥터를** 클릭하고 **CEF(일반 이벤트 형식)를** 선택한 다음 **커넥터 페이지를 엽니다.** 

1. **Syslog 에이전트를 설치하고 구성하면서**Azure, 다른 클라우드 또는 온-프레미스중 컴퓨터 유형을 선택합니다. 
   > [!NOTE]
   > 다음 단계의 스크립트는 Log Analytics 에이전트를 설치하고 컴퓨터를 Azure Sentinel 작업 영역에 연결하므로 이 컴퓨터가 다른 작업 영역에 연결되어 있지 않은지 확인합니다.
1. 컴퓨터에 높은 사용 권한(sudo)이 있어야 합니다. 다음 명령을 사용하여 컴퓨터에 파이썬이 있는지 확인하십시오.`python –version`

1. 프록시 컴퓨터에서 다음 스크립트를 실행합니다.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 스크립트가 실행되는 동안 오류 또는 경고 메시지가 없는지 확인합니다.

[2단계 계속: 보안 솔루션을 구성하여 CEF 메시지를 전달합니다.](connect-cef-solution-config.md)


## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

