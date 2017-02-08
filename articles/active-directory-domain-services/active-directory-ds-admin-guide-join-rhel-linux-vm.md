---
title: "Azure Active Directory Domain Services: 관리되는 도메인에 RHEL VM 가입 | Microsoft Docs"
description: "Red Hat Enterprise Linux 가상 컴퓨터를 Azure AD 도메인 서비스에 가입"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e345d39259f44fdd78a0f041ce823051da80b27f


---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Red Hat Enterprise Linux 7 가상 컴퓨터를 관리되는 도메인에 가입
이 문서에서는 Red Hat Enterprise Linux(RHEL) 7 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입하는 방법을 보여 줍니다.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Red Hat Enterprise Linux 가상 컴퓨터 프로비전
Azure 포털을 사용하여 RHEL 7 가상 컴퓨터를 프로비전하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
   
    ![Azure 포털 대시보드](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. 왼쪽 창에서 **새로 만들기**를 클릭하고 아래 스크린샷에 표시된 대로 검색 표시줄에 **Red Hat**을 입력합니다. 검색 결과에 Red Hat Enterprise Linux에 대한 항목이 나타납니다. **Red Hat Enterprise Linux 7.2**를 클릭합니다.
   
    ![결과에서 RHEL 선택](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. **모든 항목** 창의 검색 결과에 Red Hat Enterprise Linux 7.2 이미지가 나열됩니다. 가상 컴퓨터 이미지에 대한 자세한 내용을 보려면 **Red Hat Enterprise Linux 7.2** 를 클릭합니다.
   
    ![결과에서 RHEL 선택](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. **Red Hat Enterprise Linux 7.2** 창에 가상 컴퓨터 이미지에 대한 자세한 내용이 표시됩니다. **배포 모델 선택** 드롭다운에서 **클래식**을 선택합니다. 그런 다음 **만들기** 단추를 클릭합니다.
   
    ![이미지 세부 정보 보기](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. **VM 만들기** 창에서 새 가상 컴퓨터의 **호스트 이름**을 입력합니다. 또한 **사용자 이름** 필드에 로컬 관리자 사용자 이름을 지정하고 **암호**를 입력합니다. 로컬 관리자 사용자를 인증하는 데 SSH 키를 사용하도록 선택할 수도 있습니다. 가상 컴퓨터에 대한 **가격 책정 계층** 도 선택합니다.
   
    ![VM 만들기 - 기본 세부 정보](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. **선택적 구성**을 클릭합니다. **선택적 구성** 창에서 **네트워크**를 클릭합니다.
   
    ![VM 만들기 - 가상 네트워크 구성](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)
7. 그러면 **네트워크**라는 제목의 창이 표시됩니다. **네트워크** 창에서 **가상 네트워크**를 클릭하여 Linux VM을 배포할 가상 네트워크를 선택합니다. 이렇게 하면 **Virtual Network** 창이 열립니다. **가상 네트워크** 창에서 **기존 가상 네트워크 사용** 옵션을 선택합니다. 그런 다음 Azure AD 도메인 서비스를 사용할 수 있는 가상 네트워크를 선택합니다. 이 예제에서는 'MyPreviewVNet' 가상 네트워크를 선택했습니다.
   
    ![VM 만들기 - 가상 네트워크 선택](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. **선택적 구성** 창에서 **확인** 단추를 클릭합니다.
   
    ![VM 만들기 - 가상 네트워크 선택됨](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. 이제 가상 컴퓨터를 만들 준비가 되었습니다. **VM 만들기** 창에서 **만들기** 단추를 클릭합니다.
   
    ![VM 만들기 - 준비](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)
10. RHEL 7.2 이미지에 따라 새 가상 컴퓨터의 배포가 시작됩니다.
    
    ![VM 만들기 - 배포 시작됨](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
11. 몇 분 후 가상 컴퓨터가 성공적으로 배포되고 사용할 준비가 됩니다.
    
    ![VM 만들기 - 배포됨](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>새로 프로비전된 Linux 가상 컴퓨터에 원격으로 연결
RHEL 7.2 가상 컴퓨터가 Azure에서 프로비전되었습니다. 다음 작업은 가상 컴퓨터에 원격으로 연결하는 것입니다.

**RHEL 7.2 가상 컴퓨터에 연결** [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서의 지침을 따르세요.

나머지 단계에서는 PuTTY SSH 클라이언트를 사용하여 RHEL 가상 컴퓨터에 연결하는 것으로 가정합니다. 자세한 내용은 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 참조하세요.

1. PuTTY 프로그램을 엽니다.
2. 새로 만든 RHEL 가상 컴퓨터에 대한 **호스트 이름** 을 입력합니다. 이 예제에서 가상 컴퓨터의 호스트 이름은 'contoso-rhel.cloudapp.net'입니다. VM의 호스트 이름을 정확히 알지 못하는 경우 Azure 포털에서 VM 대시보드를 참조하세요.
   
    ![PuTTY 연결](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. 가상 컴퓨터를 만들 때 지정한 로컬 관리자 자격 증명을 사용하여 가상 컴퓨터에 로그온합니다. 이 예제에서는 로컬 관리자 계정 "mahesh"를 사용했습니다.
   
    ![PuTTY 로그인](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 가상 컴퓨터에 필요한 패키지 설치
가상 컴퓨터에 연결된 후 다음 작업은 도메인 가입에 필요한 패키지를 가상 컴퓨터에 설치하는 것입니다. 다음 단계를 수행합니다.

1. **realmd 설치:** 도메인 가입에 realmd 패키지가 사용됩니다. PuTTY 터미널에서 다음 명령을 입력합니다.
   
    sudo yum install realmd
   
    ![realmd 설치](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)
   
    몇 분 후 realmd 패키지가 가상 컴퓨터에 설치됩니다.
   
    ![realmd 설치됨](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **sssd 설치:** 도메인 가입 작업을 수행하는 데 realmd 패키지는 sssd에 종속됩니다. PuTTY 터미널에서 다음 명령을 입력합니다.
   
    sudo yum install sssd
   
    ![sssd 설치](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)
   
    몇 분 후 sssd 패키지가 가상 컴퓨터에 설치됩니다.
   
    ![realmd 설치됨](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **kerberos 설치:** PuTTY 터미널에서 다음 명령을 입력합니다.
   
    sudo yum install krb5-workstation krb5-libs
   
    ![kerberos 설치](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)
   
    몇 분 후 realmd 패키지가 가상 컴퓨터에 설치됩니다.
   
    ![Kerberos 설치됨](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 가상 컴퓨터를 관리되는 도메인에 가입
이제 필요한 패키지를 Linux 가상 컴퓨터에 설치했고 다음 작업은 가상 컴퓨터를 관리되는 도메인에 가입하는 것입니다.

1. AAD 도메인 서비스 관리되는 도메인을 검색합니다. PuTTY 터미널에서 다음 명령을 입력합니다.
   
    sudo realm discover CONTOSO100.COM
   
    ![realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)
   
    **realm discover** 로 관리되는 도메인을 찾을 수 없는 경우 가상 컴퓨터에서 도메인에 연결할 수 있는지 확인합니다(ping 시도). 또한 관리되는 도메인을 사용할 수 있는 동일한 가상 네트워크에 가상 컴퓨터를 확실히 배포했는지도 확인합니다.
2. kerberos를 초기화합니다. PuTTY 터미널에서 다음 명령을 입력합니다. 'AAD DC 관리자' 그룹에 속한 사용자를 지정해야 합니다. 이러한 사용자만 관리되는 도메인에 컴퓨터를 가입할 수 있습니다.
   
    kinit bob@CONTOSO100.COM
   
    ![kinit ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)
   
    도메인 이름을 대문자로 지정해야 하며 그렇지 않으면 kinit가 실패합니다.
3. 컴퓨터를 도메인에 가입합니다. PuTTY 터미널에서 다음 명령을 입력합니다. 이전 단계에서 지정한 동일한 사용자('kinit')를 지정합니다.
   
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
   
    ![Realm join](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

컴퓨터가 관리되는 도메인에 성공적으로 가입되면 메시지("Successfully enrolled machine in realm")가 표시됩니다.

## <a name="verify-domain-join"></a>도메인 가입 확인
컴퓨터가 관리되는 도메인에 성공적으로 가입되었는지 여부를 신속하게 확인할 수 있습니다. SSH 및 도메인 사용자 계정을 사용하여 새로 도메인에 가입된 RHEL VM에 연결한 후 사용자 계정이 올바르게 확인되었는지 확인합니다.

1. PuTTY 터미널에서 다음 명령을 입력하고 SSH를 사용하여 새로 도메인에 가입된 RHEL 가상 컴퓨터에 연결합니다. 관리되는 도메인에 속하는 도메인 계정을 사용합니다(예: 여기서는 'bob@CONTOSO100.COM').
   
    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net
2. PuTTY 터미널에서 다음 명령을 입력하여 홈 디렉터리가 올바르게 초기화되었는지 확인합니다.
   
    pwd
3. PuTTY 터미널에서 다음 명령을 입력하여 그룹 멤버 자격이 올바르게 확인되었는지 확인합니다.
   
    id

이러한 명령의 샘플 출력은 다음과 같습니다.

![도메인 가입 확인](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>도메인 가입 문제 해결
[도메인 가입 문제 해결](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) 문서를 참조하세요.

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD 도메인 서비스 - 시작 가이드](active-directory-ds-getting-started.md)
* [Windows Server 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Kerberos 설치](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows 통합 가이드](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)




<!--HONumber=Dec16_HO5-->


