---
title: 'Azure AD Connect 클라우드 프로비전 에이전트: 자동 업그레이드 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 클라우드 프로비전 에이전트의 기본 제공 자동 업그레이드 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613792"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 클라우드 프로비전 에이전트: 자동 업그레이드

자동 업그레이드 기능을 사용하면 Azure AD(Azure Active Directory) Connect 클라우드 프로비전 에이전트 설치를 항상 최신 상태인지 쉽게 확인할 수 있습니다.

에이전트 설치 위치: ‘Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe’

버전을 확인하려면 실행 파일을 마우스 오른쪽 단추로 클릭하고 속성, 자세히를 차례로 선택합니다.

![에이전트 파일 버전](media/how-to-automatic-upgrade/agent-1.png)

에이전트 업데이트 프로그램 설치 위치: ‘Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe’

버전을 확인하려면 실행 파일을 마우스 오른쪽 단추로 클릭하고 속성, 자세히를 차례로 선택합니다.

![에이전트 업데이트 프로그램 버전](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>에이전트 제거
에이전트를 제거하려면 **프로그램 제거 또는 변경** 으로 이동하여 다음을 제거합니다.

- **Microsoft Azure AD Connect 에이전트 업데이트**
- **Microsoft Azure AD Connect 프로비전 에이전트**
- **Microsoft Azure AD Connect 프로비전 에이전트 패키지**

![에이전트 제거](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)

