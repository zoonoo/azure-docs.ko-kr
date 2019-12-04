---
title: 'Azure AD Connect 클라우드 프로 비전 에이전트: 자동 업그레이드 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect 클라우드 프로 비전 에이전트의 기본 제공 자동 업그레이드 기능에 대해 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794459"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 클라우드 프로 비전 에이전트: 자동 업그레이드

**자동 업그레이드** 기능을 사용 하 여 Azure AD Connect 클라우드 프로 비전 에이전트 설치를 항상 최신 상태로 유지 하는 것이 더 쉬워졌습니다. 이 기능은 기본적으로 사용 하도록 설정 되어 있으므로 사용 하지 않도록 설정할 수 없습니다.

에이전트는 **"Program FILES\AZURE AD Connect 프로 비전 Agent\AADConnectProvisioningAgent.exe"** 에 설치 되어 있습니다.

실행 파일을 마우스 오른쪽 단추로 클릭 하 고 속성을 선택한 다음 자세히를 선택 하 여 버전을 확인할 수 있습니다.

![에이전트 파일 버전](media/how-to-automatic-upgrade/agent1.png)

에이전트 업데이트 **프로그램이 "Program FILES\AZURE AD Connect 프로 비전 에이전트 Updater\AzureADConnectAgentUpdater.exe"** 에 설치 되어 있습니다.

실행 파일을 마우스 오른쪽 단추로 클릭 하 고 속성을 선택한 다음 자세히를 선택 하 여 버전을 확인할 수 있습니다.

![에이전트 업데이트 버전](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>에이전트 제거
에이전트를 제거 하려면 **프로그램 제거 또는 변경** 으로 이동 하 여 다음을 제거 합니다.

- Microsoft Azure AD Connect 에이전트 업데이트
- Microsoft Azure AD Connect 프로비전 에이전트
- Microsoft Azure AD Connect 프로비전 에이전트 패키지

![에이전트 제거](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>다음 단계 

- [프로 비전 이란?](what-is-provisioning.md)
- [클라우드 프로 비전 Azure AD Connect 이란?](what-is-cloud-provisioning.md)

