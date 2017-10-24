---
title: "Azure Active Directory Domain Services: 관리되는 도메인에 Windows Server VM 가입 | Microsoft Docs"
description: "Windows Server 가상 컴퓨터를 Azure AD 도메인 서비스에 가입"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: ce50c678247226b629490a2bd8ba2935ed229f06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server 가상 컴퓨터를 관리되는 도메인에 가입
> [!div class="op_single_selector"]
> * [Azure Portal - Windows](active-directory-ds-admin-guide-join-windows-vm-portal.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

이 문서에서는 Azure Portal을 사용하여 Windows Server 가상 컴퓨터를 배포하는 방법을 보여줍니다. 그런 다음 가상 컴퓨터를 Azure AD Domain Services 관리되는 도메인에 가입하는 방법을 보여줍니다.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>1단계: Windows Server 가상 컴퓨터 만들기
Azure AD 도메인 서비스가 사용하도록 설정된 가상 네트워크에 가입할 Windows 가상 컴퓨터를 만들기 위해 다음 단계를 수행합니다.

1. Azure Portal[http://portal.azure.com](http://portal.azure.com)에 로그인합니다.
2. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
3. **계산**을 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다.

    ![이미지 선택](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. 마법사의 **기본 사항** 페이지에서 가상 컴퓨터의 기본 설정을 구성합니다.

    ![VM의 기본 설정 구성](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > 여기에서 가상 컴퓨터에 로그인하는 데 사용되는 로컬 관리자 계정에 대한 사용자 이름 및 암호를 입력합니다. 무차별 암호 대입 공격에 대해 가상 컴퓨터를 보호하기 위해 강력한 암호를 선택합니다. 여기에 도메인 사용자 계정 자격 증명을 입력하지 마세요.
    >

5. 가상 컴퓨터의 **크기**를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다.

    ![VM의 크기를 선택합니다.](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. 마법사의 **설정** 페이지에서 Azure AD Domain Services 관리되는 도메인을 배포한 가상 네트워크를 선택합니다. 관리되는 도메인이 배포된 서브넷이 아닌 다른 서브넷을 선택합니다. 다른 설정에서 기본값을 유지하고 **확인**을 클릭합니다.

    ![가상 컴퓨터에 대한 가상 네트워크 선택](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **적합한 가상 네트워크 및 서브넷을 선택합니다.**
    > 관리되는 도메인을 배포한 가상 네트워크 또는 가상 네트워크 피어링을 사용하여 연결된 가상 네트워크 중 하나를 선택합니다. 다른 가상 네트워크를 선택하는 경우 관리되는 도메인에 가상 네트워크를 연결할 수 없습니다.
    > 관리되는 도메인을 전용 서브넷에 배포하는 것이 좋습니다. 따라서 관리되는 도메인을 활성화한 서브넷을 선택하지 않습니다.

7. **구매** 페이지에서 설정을 검토하고 **확인**을 클릭하여 가상 컴퓨터를 배포할 수 있습니다.
8. Azure Portal 대시보드에 VM 배포를 고정합니다.

    ![완료된](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. 배포가 완료된 후에 **개요** 페이지에서 VM에 대한 정보를 확인할 수 있습니다.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>2단계: 로컬 관리자 계정을 사용하여 Windows Server 가상 컴퓨터에 연결
이제 도메인에 가입하기 위해 새로 만든 Windows Server 가상 컴퓨터에 연결합니다. 가상 컴퓨터를 만들 때 지정한 로컬 관리자 자격 증명을 사용합니다.

다음 단계를 수행하여 가상 컴퓨터에 연결합니다.

1. **개요** 페이지에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜(.rdp) 파일이 생성되고 다운로드됩니다.

    ![Windows 가상 컴퓨터에 연결](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 클릭합니다.
3. 로그인 프롬프트에서 가상 컴퓨터를 만드는 동안 지정한 **로컬 관리자 자격 증명**을 입력합니다. 예를 들어 이 예에서 'localhost\mahesh'를 사용했습니다.
4. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 예 또는 계속을 클릭하여 연결을 진행합니다.

이때 로컬 관리자 자격 증명을 사용하여 새로 만든 Windows 가상 컴퓨터에 로그인됩니다. 다음 단계는 가상 컴퓨터를 도메인에 가입하는 것입니다.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>3단계: Windows Server 가상 컴퓨터를 AAD-DS 관리되는 도메인에 가입
Windows Server 가상 컴퓨터를 AAD-DS 관리되는 도메인에 가입하려면 다음 단계를 수행합니다.

1. 2단계에서와 같이 Windows Server에 연결합니다. 시작 화면에서 **서버 관리자**를 엽니다.
2. 서버 관리자 창의 왼쪽 창에서 **로컬 서버** 를 클릭합니다.

    ![가상 컴퓨터에서 서버 관리자 시작](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. **속성** 섹션 아래에서 **작업 그룹**을 클릭합니다. **시스템 속성** 속성 페이지에서 **변경**을 클릭하여 도메인에 가입합니다.

    ![시스템 속성 페이지](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. **도메인** 텍스트 상자에 Azure AD 도메인 서비스 관리되는 도메인의 도메인 이름을 지정하고 **확인**을 클릭합니다.

    ![가입할 도메인 지정](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. 도메인에 가입하려면 자격 증명을 입력하라는 메시지가 표시됩니다. **'AAD DC 관리자' 그룹에 속한 사용자의 자격 증명을 지정** 해야 합니다. 이 그룹의 멤버만 관리되는 도메인에 컴퓨터를 가입할 수 있습니다.

    ![도메인 가입에 자격 증명 지정](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. 다음 방법 중 하나로 자격 증명을 지정할 수 있습니다.

   * **UPN 형식:(권장됨)** Azure AD에 구성된 대로 사용자 계정에 대한 UPN 접미사를 지정합니다. 이 예제에서 'bob' 사용자의 UPN 접미사는 'bob@domainservicespreview.onmicrosoft.com'입니다.
   * **SAMAccountName 형식:** SAMAccountName 형식으로 계정 이름을 지정할 수 있습니다. 이 예제에서 'bob' 사용자는 'CONTOSO100\bob'로 입력해야 합니다.

     > [!TIP]
     > **UPN 형식을 사용하여 자격 증명을 지정하는 것이 좋습니다.**
     > 사용자의 UPN 접두사가 지나치게 긴 경우(예: 'joehasareallylongname') SAMAccountName이 자동으로 생성될 수도 있습니다. Azure AD 테넌트에서 여러 사용자가 동일한 UPN 접미사(예: 'bob')를 사용하는 경우 SAMAccountName 형식이 시스템에서 자동으로 생성될 수 있습니다. 이러한 경우 UPN 형식을 사용하여 도메인에 안전하게 로그인할 수 있습니다.
     >

7. 도메인 가입에 성공한 후 도메인을 시작한다는 다음 메시지가 표시됩니다. 도메인 가입 작업을 완료하도록 가상 컴퓨터를 다시 시작합니다.

    ![도메인 시작](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>도메인 가입 문제 해결
### <a name="connectivity-issues"></a>연결 문제
가상 컴퓨터에서 도메인을 찾을 수 없는 경우 다음 문제 해결 단계를 참조하세요.

* 가상 컴퓨터가 도메인 서비스를 사용하도록 설정한 동일한 가상 네트워크에 연결되어 있는지 확인합니다. 그렇지 않은 경우 가상 컴퓨터를 도메인에 연결할 수 없으므로 도메인에 가입할 수 없습니다.
* 가상 컴퓨터가 다른 가상 네트워크에 있는 경우 이 가상 네트워크가 Domain Services를 사용하도록 설정된 가상 네트워크에 연결되는지 확인합니다.
* 관리되는 도메인의 도메인 이름을 사용하여 도메인에 ping을 시도합니다(예: 'ping contoso100.com'). 작업이 불가능한 경우 Azure AD 도메인 서비스를 사용하도록 설정된 페이지에 표시된 도메인의 IP 주소에 ping을 시도합니다(예: 'ping 10.0.0.4'). IP 주소에는 ping을 수행할 수 있지만 도메인에는 수행할 수 없는 경우 DNS가 잘못 구성되었을 수 있습니다. 도메인의 IP 주소를 가상 네트워크에 대한 DNS 서버로 구성했는지 확인합니다.
* 가상 컴퓨터에서 DNS 확인자 캐시를 플러시해 보세요('ipconfig /flushdns').

도메인 가입을 위한 자격 증명을 묻는 대화 상자가 표시되면 연결 문제는 없는 것입니다.

### <a name="credentials-related-issues"></a>자격 증명 관련 문제
자격 증명을 사용하는 데 문제가 있고 도메인에 가입할 수 없는 경우 다음 단계를 참조하세요.

* UPN 형식을 사용하여 자격 증명을 지정해 봅니다. 테넌트에서 여러 사용자가 동일한 UPN 접두사를 사용하거나 UPN 접두사가 지나치게 긴 경우 사용자 계정에 대한 SAMAccountName이 자동으로 생성될 수 있습니다. 따라서 사용자 계정에 대한 SAMAccountName 형식은 온-프레미스 도메인에서 사용하거나 예상하는 것과 다를 수 있습니다.
* 'AAD DC 관리자' 그룹에 속한 사용자 계정의 자격 증명을 사용하여 컴퓨터를 관리되는 도메인에 가입해 봅니다.
* 시작 가이드에 설명된 단계에 따라 [암호 동기화를 사용하도록 설정](active-directory-ds-getting-started-password-sync.md) 했는지 확인합니다.
* 로그인하려면 Azure AD에 구성된 대로 사용자의 UPN(예: 'bob@domainservicespreview.onmicrosoft.com')을 사용해야 합니다.
* 시작 가이드에 지정된 대로 암호 동기화가 완료될 때까지 충분히 기다려야 합니다.

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](active-directory-ds-getting-started.md)
* [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
