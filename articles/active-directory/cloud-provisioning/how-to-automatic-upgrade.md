---
title: 'Azure AD Connect 클라우드 프로 비전 에이전트: 자동 업그레이드 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 클라우드 프로 비전 에이전트의 기본 제공 자동 업그레이드 기능을 설명 합니다.
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
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549400"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 클라우드 프로 비전 에이전트: 자동 업그레이드

자동 업그레이드 기능을 사용 하면 Azure Active Directory (Azure AD) Connect 클라우드 프로 비전 에이전트 설치를 항상 최신 상태로 유지 해야 합니다. 이 기능은 기본적으로 사용 하도록 설정 되어 있으므로 사용 하지 않도록 설정할 수 없습니다.

에이전트는 "Program files\Azure AD Connect 프로 비전 Agent\AADConnectProvisioningAgent.exe"에 설치 되어 있습니다.

버전을 확인 하려면 실행 파일을 마우스 오른쪽 단추로 클릭 하 고 속성, 자세히를 차례로 선택 합니다.

![에이전트 파일 버전](media/how-to-automatic-upgrade/agent1.png)

에이전트 업데이트 프로그램이 "Program files\Azure AD Connect 프로 비전 에이전트 Updater\AzureADConnectAgentUpdater.exe"에 설치 되어 있습니다.

버전을 확인 하려면 실행 파일을 마우스 오른쪽 단추로 클릭 하 고 속성, 자세히를 차례로 선택 합니다.

![에이전트 업데이트 버전](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>에이전트 제거
에이전트를 제거 하려면 **프로그램 제거 또는 변경** 으로 이동 하 여 다음을 제거 합니다.

- **Microsoft Azure AD 연결 에이전트 업데이트 프로그램**
- **Microsoft Azure AD 연결 프로 비전 에이전트**
- **Microsoft Azure AD 연결 프로 비전 에이전트 패키지**

![에이전트 제거](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

