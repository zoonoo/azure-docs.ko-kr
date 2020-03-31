---
title: Azure AD Connect 클라우드 프로비저닝 에이전트 설치
description: 이 문서에서는 Azure AD Connect 클라우드 프로비전 에이전트를 설치하는 방법에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263349"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Azure AD Connect 클라우드 프로비저닝 에이전트 설치
이 문서에서는 Azure Active Directory(Azure AD) Connect 프로비저닝 에이전트에 대한 설치 프로세스와 Azure Portal에서 처음 구성하는 방법을 안내합니다.

>[!IMPORTANT]
>다음 설치 지침은 모든 [필수 구성 조건이](how-to-prerequisites.md) 충족되었다고 가정합니다.

Azure AD Connect 프로비저닝의 설치 및 구성은 다음 단계에서 수행됩니다.
    
- [에이전트 설치](#install-the-agent)
- [에이전트 설치 확인](#verify-agent-installation)


## <a name="install-the-agent"></a>에이전트 설치
에이전트를 설치하려면 다음 단계를 따르십시오.

1. 엔터프라이즈 관리자 권한으로 사용할 서버에 로그인합니다.
1. Azure Portal로 이동합니다. 왼쪽에서 Azure **Active 디렉터리**를 선택합니다.
1. **프로비저닝 관리(미리 보기)를** > 선택하면**모든 에이전트 검토.**
1. Azure 포털에서 Azure AD Connect 프로비전 에이전트를 다운로드합니다.

   ![온-프레미스 에이전트 다운로드](media/how-to-install/install9.png)</br>
1. Azure AD 연결 프로비전 설치 관리자(AADConnect프로비저닝 에이전트.Installer)를 실행합니다.
1. Microsoft **Azure AD 연결 프로비저닝 에이전트 패키지** 화면에서 라이선스 조건에 동의하고 **설치를**선택합니다.

   ![마이크로소프트 Azure AD 연결 프로비저닝 에이전트 패키지 화면](media/how-to-install/install1.png)</br>

1. 이 작업이 완료되면 구성 마법사가 시작됩니다. Azure AD 글로벌 관리자 계정으로 로그인합니다.
1. 활성 **디렉터리 연결** 화면에서 **디렉터리 추가를**선택합니다. 그런 다음 Active Directory 관리자 계정으로 로그인합니다. 이 작업은 온-프레미스 디렉터리를 추가합니다. **다음**을 선택합니다.

   ![활성 디렉토리 화면 연결](media/how-to-install/install3.png)</br>

1. 구성 **완료** 화면에서 **확인을**선택합니다. 이 작업은 에이전트를 등록하고 다시 시작합니다.

   ![구성 완료 화면](media/how-to-install/install4.png)</br>

1. 이 작업이 완료되면 **에이전트 구성이 성공적으로 확인되었다는** 알림이 표시됩니다. **종료**를 선택합니다.

   ![종료 단추](media/how-to-install/install5.png)</br>
1. 초기 Microsoft Azure **AD 연결 프로비저닝 에이전트 패키지** 화면이 여전히 표시되면 **닫기**를 선택합니다.

## <a name="verify-agent-installation"></a>에이전트 설치 확인
에이전트 확인은 Azure 포털 및 에이전트를 실행하는 로컬 서버에서 수행됩니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal에서 에이전트 확인
Azure에서 에이전트가 보이는지 확인하려면 다음 단계를 따르십시오.

1. Azure Portal에 로그인합니다.
1. 왼쪽에서 Azure **Active Directory** > **Azure AD 연결**을 선택합니다. 가운데에서 **프로비저닝 관리(미리 보기)를 선택합니다.**

   ![Azure portal](media/how-to-install/install6.png)</br>

1.  Azure **AD 프로비저닝(미리 보기)** 화면에서 **모든 에이전트 검토를**선택합니다.

    ![모든 에이전트 옵션 검토](media/how-to-install/install7.png)</br>
 
1. **온-프레미스 프로비저닝 에이전트** 화면에 설치한 에이전트가 표시됩니다. 해당 에이전트가 있고 *활성*으로 표시되어 있는지 확인합니다.

   ![온-프레미스 프로비저닝 에이전트 화면](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>포트 확인
Azure가 포트 443에서 수신 대기 중이고 에이전트가 포트와 통신할 수 있는지 확인하려면 다음 단계를 따르십시오.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있음을 확인합니다. 브라우저를 열고 에이전트가 설치된 서버에서 이전 URL로 이동합니다.

![포트 접근성 검증](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>로컬 서버에서 에이전트 확인
에이전트가 실행 되고 있는지 확인하려면 다음 단계를 따르십시오.

1.  관리자 계정으로 서버에 로그인합니다.
1.  로 이동하거나**실행** >  **서비스** **로** > 이동하여 서비스를**엽니다.**
1.  **서비스에서는**Microsoft **Azure AD 연결 에이전트 업데이트** 및 **Microsoft Azure AD 연결 프로비저닝 에이전트가** 있고 해당 상태가 *실행 중인지*확인합니다.

    ![서비스 화면](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>에이전트가 설치되었지만 사용자 동기화를 시작하기 전에 에이전트를 구성하고 활성화해야 합니다. 새 에이전트를 구성하려면 [Azure AD Connect 클라우드 기반 프로비저닝에 대한 새 구성 만들기를](how-to-configure.md)참조하십시오.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
 
