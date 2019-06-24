---
title: Azure AD Connect 관리자 Agent-이란 Azure AD Connect | Microsoft Docs
description: Azure AD로 온-프레미스 환경을 동기화하고 모니터링하는 데 사용된 도구를 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687245"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Azure AD Connect 관리 에이전트란? 
Azure AD Connect 관리 에이전트는 Azure Active Directory Connect의 Azure Active Directory Connect 서버에 설치할 수 있는 새 구성 요소입니다. Microsoft 지원 엔지니어가 지원 사례를 열 때 문제를 해결 하는 데 도움이 되는 Active Directory 환경의 특정 데이터를 수집 하는 것이 됩니다. 

>[!NOTE]
>관리 에이전트 설치 및 기본적으로 사용 되지 않는 합니다.  지원 사례에 도움이 되는 데이터를 수집 하려면 에이전트를 설치 해야 합니다.

설치 하는 경우 특정 요청에 대 한 Azure AD Connect 관리 에이전트 대기 데이터를 Azure Active Directory 동기화 환경에서 요청된 된 데이터를 가져오고 Azure Active Directory에 보냅니다에 대 한 microsoft 제공 됩니다 지원 엔지니어입니다. 

사용자 환경에서 Azure AD Connect 관리 에이전트를 검색 하는 정보는 어떤 방식으로든에서 저장 되지 않습니다-조사 하 고 Azure Active Directory Connect 문제 해결을 지원 하기 위해 Microsoft 지원 엔지니어에 게만 표시 됩니다. 관련된 지원 케이스를 Azure AD Connect 관리 에이전트를 열면 기본적으로 Azure AD Connect 서버에 설치 되지 됩니다. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Azure AD Connect 서버에서 Azure AD Connect 관리 에이전트 설치 
Azure AD Connect 관리 에이전트 이진 파일은 AAD 연결 서버에 배치 됩니다. 에이전트를 설치 하려면 다음을 수행 합니다. 



1. 관리자 모드에서 powershell 열기 
2. 여기서 응용 프로그램은 있는 cd "C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles" 디렉터리로 이동 
3. AADConnectAdminAgentSetup.exe 응용 프로그램 실행 
 
메시지가 표시 되 면 Azure AD 전역 관리자 자격 증명을 입력 하십시오. 

>[!NOTE]
>경우 알려진된 문제가 있는 묻는 자격 증명에 대 한 여러 번 이 문제는 다음 릴리스에서 해결될 것입니다.

에이전트를 설치한 후 서버의 제어판에서 "추가/제거 프로그램" 목록에서 다음 두 개의 새 프로그램을 볼 수 있습니다. 

![관리 에이전트](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>내 동기화 서비스의 데이터는 Microsoft 서비스 엔지니어에 게 표시 되어 있습니까? 
지원 사례를 열면 Microsoft 지원 엔지니어가 볼 수 지정된 된 사용자, Active Directory에서 관련 데이터를 Azure Active Directory Connect 서버에서 Active Directory 커넥터 공간에 대 한 Azure에서 Azure Active Directory 커넥터 공간 Active Directory Connect 서버 및 Azure Active Directory Connect 서버에서 메타 버스 합니다. 

Microsoft 지원 엔지니어에 게 시스템의 모든 데이터를 변경할 수 없습니다 및 암호를 볼 수 없습니다. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>그렇다면 내 데이터에 액세스 하려면 Microsoft 지원 엔지니어에 게 싶지? 
에이전트를 설치한 후 Microsoft 서비스 엔지니어가 지원 통화에 대 한 데이터에 액세스 하려면 원하지 않는 경우, 아래 설명 된 대로 서비스 구성 파일을 수정 하 여 기능을 비활성화할 수 있습니다. 

1.  오픈 **C:\Program Files\Microsoft Azure AD Connect 관리 Agent\AzureADConnectAdministrationAgentService.exe.config** 메모장에서.
2.  사용 안 함 **UserDataEnabled** 아래와 같이 설정 합니다. 하는 경우 **UserDataEnabled** 설정 존재 하며 true 다음 false로 설정으로 설정 됩니다. 설정이 존재 하지 않는 경우 다음 설정을 추가 합니다 아래와 같이 합니다.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  구성 파일을 저장합니다.
4.  아래와 같이 Azure AD Connect 관리 에이전트 서비스를 다시 시작

![관리 에이전트](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
