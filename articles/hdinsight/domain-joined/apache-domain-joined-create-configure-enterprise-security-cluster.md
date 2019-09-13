---
title: Azure HDInsight에서 Enterprise Security Package 클러스터 만들기 및 구성
description: Azure HDInsight에서 Enterprise Security Package 클러스터를 만들고 구성 하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 24c00d8d5db7c36746d68ad10edc4db4f76e0aac
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918755"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Azure HDInsight에서 Enterprise Security Package 클러스터 만들기 및 구성

Azure HDInsight에 대 한 Enterprise Security Package는 Active Directory 기반 인증, 다중 사용자 지원 및 Azure에서 Apache Hadoop 클러스터에 대 한 역할 기반 액세스 제어에 대 한 액세스를 제공 합니다. HDInsight ESP 클러스터는 엄격한 회사 보안 정책을 준수 하는 조직에서 중요 한 데이터를 안전 하 게 처리할 수 있도록 합니다.

이 가이드의 목표는 온-프레미스 사용자가 ESP 사용 HDInsight 클러스터에 로그인 할 수 있도록 필요한 리소스를 올바르게 구성 하는 것입니다. 이 문서에서는 Enterprise Security Package 활성화 된 Azure HDInsight 클러스터를 만드는 데 필요한 단계를 안내 합니다. 이 단계에서는 DNS (도메인 이름 서비스)를 사용 하도록 설정 된 Active Directory & Windows IaaS VM을 만드는 과정을 다룹니다. 이 서버는 **실제** 온-프레미스 환경에 대 한 대체 역할을 하며 나중에 자신의 환경에서 반복할 수 있도록 설정 및 구성 단계를 진행할 수 있습니다. 이 가이드는 또한 Azure Active Directory와 암호 해시 동기화를 사용 하 여 하이브리드 id 환경을 만드는 데 도움이 됩니다.

이 가이드는 [HDInsight의 Enterprise Security Package 사용](apache-domain-joined-architecture.md) 을 보완 하기 위한 것입니다.

사용자 환경에서이 프로세스를 사용 하기 전에 Active Directory 및 DNS (도메인 이름 서비스)를 설치 합니다. 또한 Azure Active Directory를 사용 하도록 설정 하 고 온-프레미스 사용자 계정을 Azure Active Directory에 동기화 합니다.

![아키텍처 다이어그램](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-on-premises-environment"></a>온-프레미스 환경 만들기

개요: 이 섹션에서는 Azure 빠른 배포 템플릿을 사용 하 여 새 Vm을 만들고, DNS (도메인 이름 서비스) 및 새 AD 포리스트를 구성 합니다.

1. [새 AD 포리스트를 사용 하 여 AZURE VM 만들기](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)로 이동 하 여 빠른 배포 템플릿을 봅니다.

1. **Azure에 배포를**클릭 합니다.
1. Azure 구독에 로그인합니다.
1. **새 AD 포리스트를 사용 하 여 AZURE VM 만들기** 화면에서 다음 단계를 완료 합니다.
    1. **구독** 드롭다운에서 리소스를 배포 하려는 구독을 선택 합니다.
    1. **리소스 그룹** 옆에 있는 **새로 만들기** 를 선택 하 고 이름 **OnPremADVRG** 을 입력 합니다.
    1. 템플릿 필드의 나머지 부분에 대해 다음 세부 정보를 입력 합니다.

        * **위치**: 미국 중부
        * **관리자 사용자 이름**: HDIFabrikamAdmin
        * **관리자 암호**: < YOUR_PASSWORD >
        * **도메인**: HDIFabrikam.com
        * **Dns 접두사**: hdifabrikam

        ![템플릿 Azure VM 및 AD 포리스트 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. **구매** 클릭
    1. 배포를 모니터링 하 고 완료 될 때까지 기다립니다.
    1. 리소스가 올바른 리소스 그룹 `OnPremADVRG`아래에 만들어졌는지 확인 합니다.

## <a name="configure-users-and-groups-for-cluster-access"></a>클러스터 액세스를 위한 사용자 및 그룹 구성

개요: 이 섹션에서는이 가이드의 끝부분에서 HDInsight 클러스터에 액세스 하는 사용자를 만듭니다.

1. 원격 데스크톱을 사용 하 여 도메인 컨트롤러에 연결 합니다.
    1. 처음에 언급 한 템플릿을 사용한 경우 도메인 컨트롤러는 `OnPremADVRG` 리소스 그룹에서 **adVM** 라는 VM입니다.
    1. Azure Portal > **리소스 그룹** > **OnPremADVRG** > adVMConnect > 로 이동 합니다.
    1. **Rdp** 탭을 클릭 한 다음 **rdp 파일 다운로드**를 클릭 합니다.
    1. 컴퓨터에 파일을 저장 하 고 엽니다.
    1. 자격 증명을 입력 하 라는 `HDIFabrikam\HDIFabrikamAdmin` 메시지가 표시 되 면 사용자 이름으로를 사용 하 고 관리자 계정에 대해 선택한 암호를 입력 합니다.

1. 도메인 컨트롤러 VM에서 원격 데스크톱 세션이 열리면 **서버 관리자** 대시보드에서 **Active Directory 사용자 및 컴퓨터** 를 시작 합니다. 오른쪽 위에 있는 **도구** 를 클릭 한 다음 드롭다운에서 **사용자 및 컴퓨터를 Active Directory** 합니다.

    ![서버 관리자 열린 Active Directory 관리](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. **Hdiadmin**, **hdiadmin**라는 두 명의 새 사용자를 만듭니다. 이러한 두 사용자는 HDInsight 클러스터에 로그인 하는 데 사용 됩니다.

    1. **사용자 및 컴퓨터 Active Directory** 화면에서 **작업** > **새** > **사용자**를 클릭 합니다.

        ![새 Active Directory 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. **새 개체-사용자** 화면에서 `HDIUser` **사용자 로그온 이름** 으로를 입력 하 고 **다음**을 클릭 합니다.

        ![첫 번째 관리 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 표시 되는 팝업에서 새 계정에 대 한 원하는 암호를 입력 합니다. **암호가 만료 되지 않음**이라는 확인란을 선택 합니다. HDIClick **OK**.
    1. **마침** 을 클릭 하 여 새 계정을 만듭니다.
    1. 다른 사용자 `HDIAdmin`를 만듭니다.

        ![두 번째 관리자 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. **사용자 및 컴퓨터 Active Directory** 화면에서 **작업** > **새** > **그룹**을 클릭 합니다. 새 `HDIUserGroup` 그룹으로를 만듭니다.

    ![새 Active Directory 그룹 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![새 group2 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 이전 단계에서 만든 **Hdiuser** 를 **HDIUserGroup** 에 멤버로 추가 합니다.

    1. **HDIUserGroup** 를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 클릭 합니다.
    1. **구성원** 탭으로 이동 하 고 **추가**를 클릭 합니다.
    1. `HDIUser` **선택할 개체 이름을 입력 하십시오** . 상자에를 입력 하 고 **확인**을 클릭 합니다.
    1. 다른 계정에 대해 이전 단계를 반복 합니다.`HDIAdmin`

        ![그룹에 구성원 추가](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

이제 두 명의 사용자 및 HDInsight 클러스터에 액세스 하기 위한 사용자 그룹과 함께 Active Directory 환경을 만들었습니다.

이러한 사용자는 Azure AD와 동기화 됩니다.

### <a name="create-a-new-azure-active-directory"></a>새 Azure Active Directory 만들기

1. Azure 포털에 로그인합니다.
1. **리소스 만들기** 를 클릭 하 고 **디렉터리**를 입력 합니다. **Azure Active Directory** > **만들기**를 선택 합니다.
1. **조직 이름**아래에서 **hdifabrikam** 을 입력 합니다.
1. **초기 도메인 이름**아래에 **HDIFabrikamoutlook** 을 입력 합니다.
1. **만들기**를 클릭합니다.
1. Azure Portal의 왼쪽에서 **Azure Active Directory**를 클릭 합니다.
1. 필요한 경우 **디렉터리 전환** 을 클릭 하 여 **HDIFabrikamoutlook**만든 새 디렉터리로 변경 합니다.
1. **관리** 아래에서 **사용자 지정 도메인 이름** > **사용자 지정 도메인 추가**를 클릭 합니다.
1. **사용자 지정 도메인 이름** 아래에 **HDIFabrikam.com** 를 입력 하 고 **도메인 추가**를 클릭 합니다.

![새 azure active directory 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![새 사용자 지정 도메인 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Azure AD 테 넌 트 구성

개요: 이제 온-프레미스 AD에서 클라우드로 사용자 및 그룹을 동기화 할 수 있도록 Azure AD 테 넌 트를 구성 합니다.

1. AD 테 넌 트 관리자를 만듭니다.
    1. Azure Portal에 로그인 하 고 Azure AD 테 넌 트 **Hdifabrikam** 을 선택 합니다.
    1. **관리** 아래에서 **사용자** 를 선택 하 고 **새 사용자**를 선택 합니다.
    1. 새 사용자에 대 한 다음 세부 정보를 입력 합니다.

        * 이름: fabrikamazureadmin
        * 사용자 이름:fabrikamazureadmin@hdifabrikam.com
        * 암호: 선택한 보안 암호

    1. **그룹** 섹션을 클릭 하 고 **AAD DC 관리자**를 검색 한 다음 **선택**을 클릭 합니다.

        ![그룹](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. **디렉터리 역할** 섹션을 클릭 하 고 오른쪽에서 **전역 관리자** 를 선택 합니다. **확인**을 클릭합니다.

        ![디렉터리 역할](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. 사용자에 대 한 암호를 입력 합니다. **만들기**를 클릭합니다.

1. 새로 만든 사용자 <fabrikamazureadmin@hdifabrikam.com>에 대 한 암호를 변경 하려는 경우 Id를 사용 하 여 Azure Portal에 로그인 한 다음 암호를 변경 하 라는 메시지를 표시 합니다.

## <a name="sync-on-premises-users-to-azure-ad"></a>온-프레미스 사용자를 Azure AD에 동기화

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory 연결 다운로드 및 설치

1. [Azure AD Connect 다운로드](https://www.microsoft.com/download/details.aspx?id=47594).

1. 도메인 컨트롤러에 Microsoft Azure Active Directory 연결을 설치 합니다.
    1. 이전 단계에서 다운로드 한 실행 파일을 열고 사용 조건에 동의 합니다. **계속**을 클릭합니다.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. **Express 설정 사용** 을 클릭 하 고 설치를 완료 합니다.

        ![빠른 설정 사용](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>온-프레미스 도메인 컨트롤러와 동기화 구성

1. **AZURE ad에 연결** 화면에서 azure ad에 대 한 전역 관리자의 사용자 이름 및 암호를 입력 합니다.  **다음**을 클릭 합니다. AD 테 넌 트 `fabrikamazureadmin@hdifabrikam.com` 를 구성할 때 만든 사용자 이름입니다.
    ![Azure AD에 연결](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)
1. **Active Directory Domain Services에 연결** 화면에서 엔터프라이즈 관리자 계정의 사용자 이름 및 암호를 입력 합니다.  **다음**을 클릭 합니다. 이전에 만든 사용자 `HDIFabrikam\HDIFabrikamAdmin` 이름과 일치 하는 암호입니다.

   ![Active Directory Domain Services에 연결](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. **AZURE AD 로그인 구성** 페이지에서 **다음**을 클릭 합니다.
    ![Azure AD 로그인 구성](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)
1. 구성 준비 완료 화면에서 **설치**를 클릭 합니다.
    ![설치](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)
1. **구성 완료** 화면이 표시 되 면 **끝내기**를 클릭 합니다.
    ![구성 완료](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 동기화가 완료 된 후 IAAS Active Directory에서 만든 사용자가 Azure Active Directory와 동기화 되었는지 확인 합니다.
    1. Azure 포털에 로그인합니다.
    1. **Azure Active Directory** > **hdifabrikam** > **사용자**를 선택 합니다.

### <a name="create-an-user-assigned-managed-identity"></a>사용자 할당 관리 id 만들기

Azure Active Directory Domain Services (Azure AD DS)를 구성 하는 데 사용 되는 사용자 할당 관리 id를 만듭니다. 사용자 할당 관리 id를 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 사용자 할당 관리 id에 역할 만들기, 나열, 삭제 또는 할당](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조 하세요.

1. Azure 포털에 로그인합니다.
1. **리소스 만들기** 를 클릭 하 고 **관리 되는 id**를 입력 합니다. **사용자 할당 관리 id** > **만들기**를 선택 합니다.
1. **리소스 이름**으로 **HDIFabrikamManagedIdentity** 을 입력 합니다.
1. 구독을 선택합니다.
1. **리소스 그룹** 아래에서 **새로 만들기** 를 클릭 하 고 **hdifabrikam-CentralUS**을 입력 합니다.
1. **위치**에서 **미국 중부** 를 선택 합니다.
1. **만들기**를 클릭합니다.

![사용자 할당 관리 id 새로 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 활성화

자세한 내용은 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)를 참조하세요.

1. Azure AD DS를 호스트 하는 Virtual Network을 만듭니다. 다음 powershell 코드를 실행 합니다.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure 포털에 로그인합니다.
1. **리소스 만들기**를 클릭 하 고 **도메인 서비스** 를 입력 한 다음 **Azure AD Domain Services**를 선택 합니다.
1. **기본 사항** 화면에서 다음 단계를 완료 합니다.
    1. **디렉터리 이름** 아래에서이 문서에 대해 생성 된 Azure Active Directory **hdifabrikam**을 선택 합니다.
    1. **HDIFabrikam.com**의 **DNS 도메인 이름을** 입력 합니다.
    1. 구독을 선택합니다.
    1. 리소스 그룹 **Hdifabrikam-CentralUS** 및 **미국 중부**의 **위치** 를 지정 합니다.

        ![Azure AD-DS 기본 정보](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. **네트워크** 화면 전체에서 이전 powershell 스크립트를 사용 하 여 만든 네트워크 (**HDIFABRIKAM-VNET**) 및 서브넷 (**aadds-서브넷**)을 선택 합니다. 또는 **새로 만들기** 옵션을 사용 하 여 이제 가상 네트워크를 만들 수 있습니다.

    ![네트워크 선택](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. **관리자 그룹** 화면에서이 그룹을 관리 하기 위해 **AAD DC 관리자** 라는 그룹이 이미 생성 되었다는 알림이 표시 됩니다. 필요에 따라이 그룹의 멤버 자격을 수정할 수 있지만이 문서의 단계에서는 필요 하지 않습니다. **확인**을 클릭합니다.

    ![관리자 그룹 보기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. **동기화** 화면에서 **모두** 를 선택 하 여 전체 동기화를 사용 하도록 설정 하 고 **확인**을 클릭 합니다.

    ![동기화 사용](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. **요약** 화면에서 AZURE AD DS에 대 한 세부 정보를 확인 하 고 **확인**을 클릭 합니다.

    ![세부 정보 확인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. Azure AD-DS를 사용하도록 설정하면 로컬 DNS(도메인 이름 서비스) 서버가 AD VM(Virtual Machine)에서 실행됩니다.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크 구성

이 섹션의 단계는 사용자 지정 DNS 서버를 사용 하도록 Azure AD DS 가상 네트워크 (**Hdifabrikam-AADDSVNET**)를 구성 하는 데 도움이 됩니다.

1. 사용자 지정 DNS 서버의 IP 주소를 찾습니다. **HDIFabrikam.com** AD DS 리소스를 클릭 하 고 **관리**   아래에서 **속성** 을 클릭 한 다음 **Virtual Network ip 주소**아래에 나열 된 ip 주소를 확인 합니다.

    ![Azure AD DS에 대 한 사용자 지정 DNS IP 주소 찾기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. **Hdifabrikam-aaddsvnet** 을 사용자 지정 ip `10.0.0.4` 및 `10.0.0.5`로 구성 합니다.

    1.   **설정**범주아래에서DNS서버를선택 합니다. 그런 다음 **사용자 지정**옆의 라디오 단추를 클릭 하 고 아래의 텍스트 상자에 첫 번째 IP 주소 (10.0.0.4)를 입력 한 다음 **저장**을 클릭 합니다.
    1. 동일한 단계를 사용 하 여 추가 IP 주소 (10.0.0.5)를 추가 합니다.

1. 이 시나리오에서 Azure AD-DS는 IP 주소 10.0.0.4 및 10.0.0.5를 사용 하도록 구성 되었으며, 아래 이미지에 표시 된 것 처럼 AADDS에 동일한 IP 주소를 설정 합니다.

    ![사용자 지정 DNS 서버 보기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP 트래픽 보안

LDAP (Lightweight Directory Access Protocol)는 Active Directory에서 읽고 쓰는 데 사용 됩니다. SSL(Secure Sockets Layer) (SSL)/TLS (전송 계층 보안) 기술을 사용 하 여 LDAP 트래픽을 기밀 및 안전 하 게 만들 수 있습니다. 올바른 형식의 인증서를 설치 하 여 SSL (LDAPS)을 통해 LDAP를 사용 하도록 설정할 수 있습니다.

보안 LDAP에 대 한 자세한 내용은 [Azure AD Domain Services 관리 되는 도메인에 대 한 보안 ldap (LDAPS) 구성](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)을 참조 하세요.

이 섹션에서는 자체 서명 된 인증서를 만들고, 인증서를 다운로드 하 고, **hdifabrikam** AZURE AD DS 관리 되는 도메인에 대해 보안 LDAP (LDAPS)를 구성 합니다.

다음 스크립트는 hdifabrikam에 대 한 인증서를 만듭니다. 인증서는 "LocalMachine" 경로 아래에 저장 됩니다.

> [!Note] 
> 유효한 PKCS \#10 요청을 만드는 유틸리티 또는 응용 프로그램을 사용 하 여 SSL 인증서 요청을 구성할 수 있습니다.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

인증서가 컴퓨터\'의 개인 저장소에 설치 되어 있는지 확인 합니다. 다음 단계를 완료합니다.

1. MMC (Microsoft Management Console)를 시작 합니다.
1. 로컬 컴퓨터에서 인증서를 관리 하는 인증서 스냅인을 추가 합니다.
1.  **인증서 (로컬 컴퓨터)** 를 확장 하 고 **개인**을 확장 한 다음 **인증서**를 확장 합니다. 새 인증서가 개인 저장소에 있어야 합니다. 이 인증서는 정규화 된 호스트 이름에 발급 됩니다.

    ![인증서 만들기 확인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 오른쪽 창에서 이전 단계에서 만든 인증서를 마우스 오른쪽 단추로 클릭 하 고 **모든 작업**을 가리킨 다음 **내보내기**를 클릭 합니다.

1.  **개인 키** 내보내기 페이지에서 **예, 개인 키를 내보냅니다. 키**를 클릭 합니다. 키를 가져올 컴퓨터에서 암호화 된 메시지를 읽으려면 개인 키가 필요 합니다.

    ![개인 키 내보내기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1.  **내보내기 파일 형식** 페이지에서 기본 설정을 그대로 두고 **다음**을 클릭 합니다. 
1.  **암호** 페이지에서 개인 키에 대 한 암호를 입력 하 고 암호화에 TripleDES-SHA1을 선택 하 고 다음을 클릭 합니다. 
1.  **내보낼** 파일 페이지에서 내보낸 인증서 파일의 경로와 이름을 입력 하 고 **다음**을 클릭 합니다.
1. 파일 이름은 .pfx 확장명 이어야 하며,이 파일은 Azure Portal에 구성 되어 보안 연결을 설정 합니다.
1. Azure AD Domain Services 관리 되는 도메인에 대해 보안 LDAP (LDAPS)를 사용 하도록 설정 합니다.
    1. Azure Portal에서 도메인 **HDIFabrikam.com** 를 선택 합니다.
    1. **관리**에서 **보안 LDAP** 를 클릭 합니다.
    1. **보안 LDAP** 화면의 **보안 LDAP**아래에서 **사용** 을 클릭 합니다.
    1. 컴퓨터에서 내보낸 .pfx 인증서 파일을 찾습니다.
    1. 인증서 암호를 입력 합니다.

    ![보안 ldap 사용](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 보안 LDAP를 사용 하도록 설정 했으므로 포트 636을 사용 하도록 설정 하 여 연결할 수 있는지 확인 합니다.
    1. **Hdifabrikam-CentralUS** 리소스 그룹에서 네트워크 보안 그룹 **AADDS-nsg** 를 클릭 합니다.
    1. **설정** 아래에서 **인바운드 보안 규칙** > **추가**를 클릭 합니다.
    1. **인바운드 보안 규칙 추가** 화면에서 다음 속성을 입력 하 고 **추가**를 클릭 합니다.

        | 속성 | 값 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Name | Port_LDAP_636 |

    ![인바운드 보안 규칙](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`는 사용자 할당 관리 id 이며,이 Id에서 도메인 서비스 작업을 읽고, 만들고, 수정 하 고, 삭제할 수 있도록 하는 관리 id에 대해 HDInsight Domain Services 기여자 역할을 사용 하도록 설정 됩니다.

    ![사용자가 할당한 관리 ID 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Enterprise Security Package 사용 가능한 HDInsight 클러스터 만들기

이 단계를 수행 하려면 다음 필수 구성 요소가 필요 합니다.

1. `HDIFabrikam-WestUS` 위치`West US`에 새 리소스 그룹을 만듭니다.
1. ESP 사용 HDInsight 클러스터를 호스트 하는 가상 네트워크를 만듭니다.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Aadds (`HDIFabrikam-AADDSVNET`)를 호스트 하는 Virtual Network와 ESP 사용 HDInsight`HDIFabrikam-HDIVNet`클러스터를 호스팅할 Virtual Network () 간에 피어 관계를 만듭니다. 다음 powershell 코드를 사용 하 여 이러한 두 가상 네트워크를 피어 링 합니다.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 사용자 관리 id **HDIFabrikamManagedIdentity**로 구성 된 새 Azure Data Lake Storage Gen2 계정 ( **Hdigen2store**)을 만듭니다. 사용자 관리 id를 사용 하 여 사용 하도록 설정 된 Data Lake Storage Gen2 계정을 만드는 방법에 대 한 자세한 내용은 [Azure HDInsight 클러스터와 함께 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조 하세요.

1. **Hdifabrikam-AADDSVNET** 가상 네트워크에서 사용자 지정 DNS를 설정 합니다.
    1. Azure Portal > **리소스 그룹** > **OnPremADVRG** > **hdifabrikam-aaddsvnet** > **DNS 서버**로 이동 합니다.
    1. **사용자 지정** 을 선택 `10.0.0.4` 하 `10.0.0.5`고 및을 입력 합니다.
    1. **Save**을 클릭합니다.

        ![사용자 지정 DNS 설정 저장](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 새 ESP 사용 HDInsight Spark 클러스터를 만듭니다.
    1. **사용자 지정 (크기, 설정, 앱)** 을 클릭 합니다.
    2. 섹션 1 **기본 사항**에 대 한 원하는 세부 정보를 입력 합니다. **클러스터 유형이** **Spark 2.3 (hdi 3.6)** 이 고 **리소스 그룹이** **hdifabrikam-CentralUS** 인지 확인 합니다.

    1. 섹션 2 **보안 + 네트워킹**에서 다음 단계를 완료 합니다.
        1. **Enterprise Security Package**아래에서 **사용** 을 클릭 합니다.
        1. **클러스터 관리자 사용자** 를 클릭 하 고 이전에 온-프레미스 관리 사용자로 만든 **hdiadmin** 계정을 선택 합니다. **선택**을 클릭합니다.

        1. **클러스터 액세스 그룹** 을 클릭 한 다음 **HDIUserGroup**를 선택 합니다. 향후이 그룹에 추가 하는 모든 사용자는 HDInsight 클러스터에 액세스할 수 있습니다.

            ![클러스터 액세스 그룹 선택](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 클러스터 구성의 다른 단계를 완료 하 고 **클러스터 요약**에서 세부 정보를 확인 합니다. **만들기**를 클릭합니다.

1. 관리자 사용자 이름 `https://CLUSTERNAME.azurehdinsight.net` `hdiadmin@hdifabrikam.com` 및 암호를 사용 하 여에서 새로 만든 클러스터에 대 한 Ambari UI에 로그인 합니다.

    ![Ambari에 로그인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 클러스터 대시보드에서 **역할** 을 클릭 합니다.
1. **역할** 페이지에서 그룹 **hdiusergroup** 를 입력 하 여 **역할 할당**아래의 **클러스터 관리자** 역할에 할당 합니다.

    ![Hdiusergroup에 클러스터 관리자 역할 할당](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. SSH 클라이언트를 열고 온-프레미스 Active Directory에서 이전에 만든 **hdiuser** 를 사용 하 여 클러스터에 로그인 합니다.

    ![SSH를 사용 하 여 클러스터에 로그인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

이 계정으로 로그인 할 수 있는 경우 온-프레미스 active directory와 동기화 하도록 ESP 클러스터를 올바르게 구성 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Enterprise Security Package를 사용하여 Apache Hadoop 보안 소개](hdinsight-security-overview.md)
