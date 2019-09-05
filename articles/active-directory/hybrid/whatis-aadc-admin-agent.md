---
title: Azure AD Connect 관리 에이전트-Azure AD Connect | Microsoft Docs
description: Azure AD로 온-프레미스 환경을 동기화하고 모니터링하는 데 사용된 도구를 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305213"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Azure AD Connect 관리 에이전트란? 
Azure AD Connect 관리 에이전트는 Azure Active Directory Connect 서버에 설치할 수 있는 Azure Active Directory Connect의 새로운 구성 요소입니다. 지원 사례를 열 때 Microsoft 지원 엔지니어가 문제를 해결 하는 데 도움이 되는 Active Directory 환경에서 특정 데이터를 수집 하는 데 사용 됩니다. 

>[!NOTE]
>관리 에이전트는 기본적으로 설치 및 사용 하도록 설정 되어 있지 않습니다.  지원 사례를 지원 하기 위해 데이터를 수집 하기 위해 에이전트를 설치 해야 합니다.

Azure AD Connect 관리 에이전트는 설치 된 경우 Azure Active Directory 데이터에 대 한 특정 요청을 대기 하 고, 동기화 환경에서 요청 된 데이터를 가져오고, Microsoft 지원에 제공 되는 Azure Active Directory으로 보냅니다. 엔지니어. 

Azure AD Connect 관리 에이전트가 사용자 환경에서 검색 하는 정보는 어떤 방식으로든 저장 되지 않습니다. Microsoft 지원 엔지니어에 게 표시 되어 문제를 조사 하 고 문제를 해결 하는 데 도움을 받을 수 있습니다 Azure Active Directory Connect Azure AD Connect 관리 에이전트에서 연 관련 지원 사례가 기본적으로 Azure AD Connect 서버에 설치 되어 있지 않습니다. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Azure AD Connect 서버에 Azure AD Connect 관리 에이전트 설치 

필수 구성 요소:
1.  서버에 Azure AD Connect 설치 되어 있습니다.
2.  서버에 Azure AD Connect Health 설치 되어 있습니다.

![관리 에이전트](media/whatis-aadc-admin-agent/adminagent0.png)

Azure AD Connect 관리 에이전트 이진 파일은 AAD Connect 서버에 배치 됩니다. 에이전트를 설치 하려면 다음을 수행 합니다.

1.  관리자 모드에서 powershell 열기
2.  응용 프로그램이 있는 디렉터리로 이동 합니다. "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.  ConfigureAdminAgent를 실행 합니다.

메시지가 표시 되 면 Azure AD 전역 관리자 자격 증명을 입력 하세요. Azure AD Connect 설치 하는 동안 입력 한 것과 동일한 자격 증명 이어야 합니다.

에이전트가 설치 되 면 서버의 제어판에 있는 "프로그램 추가/제거" 목록에 다음과 같은 두 개의 새 프로그램이 표시 됩니다. 

![관리 에이전트](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Microsoft 서비스 엔지니어에 게 동기화 서비스에 표시 되는 데이터는 무엇 인가요? 
지원 사례를 열 때 Microsoft 지원 엔지니어가 Active Directory의 관련 데이터, Azure Active Directory Connect 서버의 Active Directory 커넥터 공간, Azure의 Azure Active Directory 커넥터 공간을 볼 수 있습니다. Azure Active Directory Connect 서버에서 서버와 메타 버스를 연결 Active Directory 합니다. 

Microsoft 지원 엔지니어가 시스템의 데이터를 변경할 수 없으며 암호를 볼 수 없습니다. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Microsoft 지원 엔지니어가 내 데이터에 액세스 하는 것을 원하지 않는 경우 어떻게 하나요? 
에이전트가 설치 되 면 Microsoft 서비스 엔지니어가 지원 전화를 위해 데이터에 액세스 하는 것을 원하지 않는 경우 아래 설명 된 대로 서비스 구성 파일을 수정 하 여 기능을 사용 하지 않도록 설정할 수 있습니다. 

1.  메모장에서 **C:\Program Files\Microsoft Azure AD Connect 관리 Agent\AzureADConnectAdministrationAgentService.exe.config** 를 엽니다.
2.  아래와 같이 **Userdataenabled** 설정을 사용 하지 않도록 설정 합니다. **Userdataenabled** 설정이 있고가 true로 설정 된 경우 false로 설정 합니다. 설정이 없는 경우 아래와 같이 설정을 추가 합니다.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  구성 파일을 저장합니다.
4.  아래 표시 된 대로 Azure AD Connect 관리 에이전트 서비스를 다시 시작 합니다.

![관리 에이전트](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
