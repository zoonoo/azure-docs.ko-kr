---
title: Azure AD Connect 프로 비전 에이전트 설치
description: 이 항목에서는 프로 비전 에이전트를 설치 하는 방법을 단계별로 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf479d4962f6d7aa9a0ba43b48f99bd67566fb6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794485"
---
# <a name="install-azure-ad-connect-provisioning-agent"></a>프로 비전 에이전트 Azure AD Connect 설치
이 문서에서는 Azure AD Connect 프로 비전 에이전트에 대 한 설치 프로세스와 Azure Portal에서 초기에 구성 하는 방법을 안내 합니다.

>[!IMPORTANT]
>다음 설치 지침에서는 모든 [필수 구성 요소가](how-to-prerequisites.md) 충족 된 것으로 가정 합니다.

Azure AD Connect 프로 비전을 설치 하 고 구성 하는 작업은 다음 단계에서 수행 됩니다.
    
- [에이전트 설치](#install-the-agent)
- [에이전트 설치 확인](#verify-agent-installation)


## <a name="install-the-agent"></a>에이전트 설치

1. 엔터프라이즈 관리자 권한으로 사용할 서버에 로그인 합니다.
2. Azure Portal로 이동 하 여 왼쪽에 있는 Azure Active Directory를 선택 합니다.
3. **프로 비전 관리 (미리 보기)** 를 클릭 하 고 **모든 에이전트 검토**를 선택 합니다.
3. Azure Portal에서 Azure AD Connect 프로 비전 에이전트를 다운로드 합니다.
![시작 화면](media/how-to-install/install9.png)</br>
3. Azure AD Connect 프로 비전 (AADConnectProvisioningAgent) 실행
3. 시작 화면에서 사용 조건에 **동의** 하 고 **설치**를 클릭 합니다.</br>
![시작 화면](media/how-to-install/install1.png)</br>

4. 이 작업이 완료 되 면 구성 마법사가 시작 됩니다.  Azure AD 전역 관리자 계정으로 로그인 합니다.
5. **Active Directory 연결** 화면에서 **디렉터리 추가** 를 클릭 한 다음 Active Directory 관리자 계정으로 로그인 합니다.  이 작업을 수행 하면 온-프레미스 디렉터리가 추가 됩니다.  **다음**을 누릅니다.</br>
![시작 화면](media/how-to-install/install3.png)</br>

6. **구성 완료** 화면에서 **확인**을 클릭 합니다.  이 작업은 에이전트를 등록 하 고 다시 시작 합니다.</br>
![시작 화면](media/how-to-install/install4.png)</br>

7. 이 작업이 완료 되 면 **성공적으로 확인 되었다는** 알림이 표시 됩니다.  **끝내기**를 클릭할 수 있습니다.</br>
![시작 화면](media/how-to-install/install5.png)</br>
8. 초기 시작 화면이 계속 표시 되 면 **닫기**를 클릭 합니다.


## <a name="verify-agent-installation"></a>에이전트 설치 확인
에이전트 확인은 에이전트를 실행 하는 로컬 서버와 Azure Portal에서 수행 됩니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal 에이전트 확인
Azure에서 에이전트가 표시 되는지 확인 하려면 다음 단계를 수행 합니다.

1. Azure 포털에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory**를 선택 하 고 **Azure AD Connect** 를 클릭 한 다음 센터에서 **프로 비전 관리 (미리 보기)** 를 선택 합니다.</br>
![Azure 포털](media/how-to-install/install6.png)</br>

3.  **AZURE AD 프로 비전 (미리 보기)** 화면에서 **모든 에이전트 검토**를 클릭 합니다.
Azure AD 프로 비전](media/how-to-install/install7.png) ![</br>
 
4. **온-프레미스 프로 비전 에이전트 화면** 에서 설치한 에이전트가 표시 됩니다.  해당 에이전트가 있고 **활성**상태로 표시 되어 있는지 확인 합니다.
![프로 비전 에이전트](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>포트 확인
Azure가 포트 443에서 수신 대기 하 고 에이전트와 통신할 수 있는지 확인 하려면 다음을 사용할 수 있습니다.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있는지 확인 합니다.  브라우저를 열고 에이전트가 설치 된 서버에서 위의 URL로 이동 합니다.
![서비스](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>로컬 서버에서
에이전트가 실행 되 고 있는지 확인 하려면 다음 단계를 수행 합니다.

1.  관리자 계정으로 서버에 로그온 합니다.
2.  서비스를 탐색 하거나 Start/Run/services.msc로 이동 하 여 **서비스** 를 엽니다.
3.  **서비스** 에서 **연결 에이전트 업데이트** 프로그램 및 **Microsoft Azure AD 연결 프로 비전 에이전트가** 있고 상태가 **실행**중인지 Microsoft Azure AD 확인 합니다.
![서비스](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>에이전트가 설치 되어 있지만 사용자 동기화를 시작 하기 전에 구성 하 고 사용 하도록 설정 해야 합니다.  새 에이전트를 구성 하려면 [새 에이전트 구성 Azure AD Connect 프로 비전](how-to-configure.md)을 참조 하세요.



## <a name="next-steps"></a>다음 단계 

- [프로 비전 이란?](what-is-provisioning.md)
- [클라우드 프로 비전 Azure AD Connect 이란?](what-is-cloud-provisioning.md)
 
