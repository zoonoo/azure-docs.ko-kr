---
title: 'Azure AD Connect 클라우드 프로비저닝 에이전트: 자동 업그레이드 | 마이크로 소프트 문서'
description: 이 문서에서는 Azure AD Connect 클라우드 프로비전 에이전트의 기본 제공 자동 업그레이드 기능에 대해 설명합니다.
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
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190316"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 클라우드 프로비전 에이전트: 자동 업그레이드

Azure Active Directory(Azure AD) Connect 클라우드 프로비저닝 에이전트 설치가 자동 업그레이드 기능을 사용하면 항상 최신 상태로 유지되도록 할 수 있습니다.

에이전트가 여기에 설치되어 있습니다: "프로그램 파일\Azure AD 연결 프로비저닝 에이전트\AADConnectProvisioning.exe"

버전을 확인하려면 실행 파일을 마우스 오른쪽 단추로 클릭하고 속성을 선택한 다음 세부 정보를 선택합니다.

![에이전트 파일 버전](media/how-to-automatic-upgrade/agent1.png)

에이전트 업데이트가 여기에 설치됩니다: "프로그램 파일\Azure AD 연결 프로비저닝 에이전트 업데이트\AzureADConnectAgentUpdater.exe"

버전을 확인하려면 실행 파일을 마우스 오른쪽 단추로 클릭하고 속성을 선택한 다음 세부 정보를 선택합니다.

![에이전트 업데이터 버전](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>에이전트 제거
에이전트를 제거하려면 **제거 또는 프로그램 변경으로** 이동하여 다음을 제거합니다.

- **Microsoft Azure AD Connect 에이전트 업데이트**
- **Microsoft Azure AD Connect 프로비전 에이전트**
- **Microsoft Azure AD Connect 프로비전 에이전트 패키지**

![에이전트 제거](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

