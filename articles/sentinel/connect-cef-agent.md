---
title: Azure 센티널 Preview에 CEF 데이터를 연결 하는 에이전트 배포 | Microsoft Docs
description: Azure 센티널에 CEF 데이터를 연결 하는 에이전트를 배포 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5451382763195172b48e93ef2ea859552b05d154
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615373"
---
# <a name="step-1-deploy-the-agent"></a>1 단계: 에이전트 배포


이 단계에서는 Azure 센티널과 보안 솔루션 간의 프록시로 사용할 Linux 컴퓨터를 선택 해야 합니다. 프록시 컴퓨터에서 다음을 수행 하는 스크립트를 실행 해야 합니다.
- Log Analytics 에이전트를 설치 하 고 Syslog 메시지를 수신 하는 데 필요한 대로 구성 합니다.
- Tcp 포트 514를 사용 하 여 Syslog 메시지를 수신 하 고, TCP 포트 25226을 사용 하 여 CEF 메시지만 Log Analytics 에이전트로 전달 하도록 Syslog 데몬을 구성 합니다.
- 는 데이터를 수집 하 여 Azure 센티널로 안전 하 게 전송 하도록 Syslog 에이전트를 설정 하 고,이를 구문 분석 하 고 보강 합니다.
 
## <a name="deploy-the-agent"></a>에이전트 배포
 
1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **Cef (일반 이벤트 형식)** 를 선택한 다음 **커넥터 페이지를 엽니다**. 

1. **Syslog 에이전트 설치 및 구성**에서 컴퓨터 유형 (Azure, 다른 클라우드 또는 온-프레미스)을 선택 합니다. 
   > [!NOTE]
   > 다음 단계의 스크립트는 Log Analytics 에이전트를 설치 하 고 컴퓨터를 Azure 센티널 작업 영역에 연결 하기 때문에이 컴퓨터가 다른 작업 영역에 연결 되어 있지 않은지 확인 합니다.
1. 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다. 다음 명령을 사용 하 여 컴퓨터에 Python이 있는지 확인 합니다. `python –version`

1. 프록시 컴퓨터에서 다음 스크립트를 실행 합니다.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 스크립트를 실행 하는 동안 오류 또는 경고 메시지가 표시 되지 않는지 확인 합니다.

[2 단계: CEF 메시지를 전달 하도록 보안 솔루션 구성](connect-cef-solution-config.md) 을 계속 진행 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

