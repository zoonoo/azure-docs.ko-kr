---
title: Azure AD Connect 클라우드 프로비저닝 에이전트 설치
description: 이 문서에서는 Azure AD Connect 클라우드 프로비저닝 에이전트를 설치하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb322805ac3368dd6ed8e193875e083b27195e1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695285"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Azure AD Connect 클라우드 프로비저닝 에이전트 설치
이 문서에서는 Azure AD(Azure Active Directory) Connect 프로비저닝 에이전트의 설치 프로세스와 Azure Portal에 이를 처음으로 구성하는 방법을 안내합니다.

>[!IMPORTANT]
>다음 설치 지침에서는 모든 [사전 요구 사항](how-to-prerequisites.md)을 충족한다고 가정합니다.

Azure AD Connect 프로비저닝을 설치하고 구성하는 작업은 다음 단계에 따라 수행됩니다.
    
- [에이전트 설치](#install-the-agent)
- [에이전트 설치 확인](#verify-agent-installation)


## <a name="install-the-agent"></a>에이전트 설치
에이전트를 설치하려면 다음 단계를 따르세요.

1. 사용할 서버에 엔터프라이즈 관리자 권한으로 로그인합니다.
1. Azure Portal에 로그인 한 후 **Azure Active Directory** 로 이동 합니다.
1. 왼쪽 메뉴에서 **Azure AD Connect** 를 선택 합니다.
1. **프로비저닝 관리(미리 보기)**  > **모든 에이전트 검토** 를 선택합니다.
1. Azure Portal에서 Azure AD Connect 프로비저닝 에이전트를 다운로드합니다.

   ![온-프레미스 에이전트 다운로드](media/how-to-install/install-9.png)</br>
1. Azure AD Connect 프로비저닝 설치 관리자를 실행합니다(AADConnectProvisioningAgent.Installer).
1. **Microsoft Azure AD Provisioning Agent Package** 화면에서 사용 조건에 동의하고 **설치** 를 선택합니다.

   ![Microsoft Azure AD Connect Provisioning Agent Package 화면](media/how-to-install/install-1.png)</br>

1. 이 작업이 완료되면 구성 마법사가 시작됩니다. Azure AD 글로벌 관리자 계정으로 로그인합니다.
1. **Active Directory 연결** 화면에서 **디렉터리 추가** 를 선택합니다. 그런 다음, Active Directory 관리자 계정으로 로그인합니다. 이 작업을 수행하면 온-프레미스 디렉터리가 추가됩니다. **다음** 을 선택합니다.

   ![Active Directory 연결 화면](media/how-to-install/install-3.png)</br>

1. **구성 완료** 화면에서 **확인** 을 선택합니다. 이 작업은 에이전트를 등록하고 다시 시작합니다.

   ![구성 완료 화면](media/how-to-install/install-4a.png)</br>

1. 이 작업이 완료되면 **에이전트 구성이 성공적으로 확인되었습니다** 라는 알림이 표시됩니다. **끝내기** 를 선택합니다.

   ![끝내기 단추](media/how-to-install/install-5.png)</br>
1. 초기 **Microsoft Azure AD Connect Provisioning Agent Package** 화면이 계속 표시되는 경우 **닫기** 를 선택합니다.

## <a name="verify-agent-installation"></a>에이전트 설치 확인
에이전트 확인은 Azure Portal 및 에이전트를 실행하는 로컬 서버에서 수행됩니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal에서 에이전트 확인
Azure에서 에이전트가 표시되는지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **Azure AD Connect** 를 선택합니다. 가운데에서 **프로비저닝 관리(미리 보기)** 를 선택합니다.

   ![Azure portal](media/how-to-install/install-6.png)</br>

1.  **Azure AD 프로비저닝(미리 보기)** 화면에서 **모든 에이전트 검토** 를 선택합니다.

    ![모든 에이전트 검토 옵션](media/how-to-install/install-7.png)</br>
 
1. **온-프레미스 프로비저닝 에이전트** 화면에 설치한 에이전트가 표시됩니다. 해당 에이전트가 있고 *활성* 으로 표시되어 있는지 확인합니다.

   ![온-프레미스 프로비저닝 에이전트 화면](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>로컬 서버에서 에이전트 확인
에이전트가 실행되는지 확인하려면 다음 단계를 수행합니다.

1.  관리자 계정으로 서버에 로그인합니다.
1.  **서비스** 를 탐색하거나 **시작** > **실행** > **Services.msc** 로 이동하여 서비스를 엽니다.
1.  **서비스** 아래에서 **Microsoft Azure AD Connect 에이전트 업데이트 프로그램** 및 **Microsoft Azure AD Connect Provisioning Agent** 가 있고 상태가 *실행 중* 인지 확인합니다.

    ![서비스 화면](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>에이전트가 설치되었지만 에이전트가 사용자 동기화를 시작하기 전에 에이전트를 구성하고 사용하도록 설정해야 합니다. 새 에이전트를 구성하려면 [Azure AD Connect 클라우드 기반 프로비저닝에 대한 새 구성 만들기](how-to-configure.md)를 참조하세요.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
 
