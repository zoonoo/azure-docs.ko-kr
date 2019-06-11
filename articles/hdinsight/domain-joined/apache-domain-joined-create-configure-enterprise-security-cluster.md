---
title: 만들기 및 Azure HDInsight에서 엔터프라이즈 보안 패키지 클러스터 구성
description: 만들기 및 Azure HDInsight에서 엔터프라이즈 보안 패키지 클러스터를 구성 하는 방법을 알아봅니다
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: 5b5b83fe0028e43ca35bf883b29cb71bad6ca2c8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253131"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>만들기 및 Azure HDInsight에서 엔터프라이즈 보안 패키지 클러스터 구성

Azure HDInsight 용 엔터프라이즈 보안 패키지는 Azure에서 Apache Hadoop 클러스터에 대해 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 access control 액세스를 제공합니다. ESP HDInsight 클러스터에 중요 한 데이터를 안전 하 게 처리 하는 데 엄격한 회사 보안 정책을 준수 하는 조직은 있도록 합니다.

이 가이드의 목적은 올바르게 구성 되어 필요한 리소스가 있도록 하는 온-프레미스 사용자가 로그인 할 수는 ESP HDInsight 클러스터를 사용 하도록 설정 합니다. 이 문서에서는 Azure HDInsight 클러스터를 사용 하도록 설정 하는 엔터프라이즈 보안 패키지를 만드는 데 필요한 단계를 안내 합니다. Active Directory 및 도메인 이름 서비스 (DNS)가 사용 하도록 설정 된 Windows IaaS VM을 만드는 단계를 설명 합니다. 이 서버는 역할을 대체 하 **실제** 온-프레미스 환경 및 고유한 환경에서 이후에 반복할 수 있도록 설치 및 구성 단계를 진행할 수 있습니다. 이 가이드는 데도 도움이 암호 해시 동기화를 사용 하 여 Azure Active Directory를 사용 하 여 하이브리드 identity 환경을 만듭니다.

이 가이드를 보완 하려는 [HDInsight에서 사용 하 여 엔터프라이즈 보안 패키지](apache-domain-joined-architecture.md)

이 프로세스를 사용자 고유의 환경에서 사용 하기 전에 Active Directory 및 도메인 이름 서비스 (DNS)를 설정 합니다. 또한 Azure Active Directory에 온-프레미스 사용자 계정을 Azure Active Directory와 동기화를 사용 하도록 설정 합니다.

![아키텍처 다이어그램](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>온-프레미스 환경 만들기

개요: 이 섹션에서는 Azure 빠른 배포 템플릿을 사용 하 여 새 Vm을 만들고, 새 AD 포리스트 및 도메인 이름 서비스 (DNS)를 구성 하기는 있습니다.

1. 로 이동 [새 AD 포리스트를 사용 하 여 Azure VM을 만드는](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), 빠른 배포 템플릿을 볼 수 있습니다.

1. 클릭할 **Azure에 배포**합니다.
1. Azure 구독에 로그인합니다.
1. 에 **새 AD 포리스트를 사용 하 여 Azure VM을 만드는** 화면에서 다음 단계를 완료 합니다.
    1. 배포 된 리소스를 만들려는 구독을 선택 합니다 **구독** 드롭다운 합니다.
    1. 선택 **새로 만들기** 옆에 **리소스 그룹** 이름을 입력 하 고 **OnPremADVRG**
    1. 템플릿 필드의 나머지 부분에 다음 세부 정보를 입력 합니다.

        * **위치**: 미국 중부
        * **관리자 사용자 이름**: HDIFabrikamAdmin
        * **관리자 암호**: < YOUR_PASSWORD >
        * **도메인**: HDIFabrikam.com
        * **Dns 접두사**: hdifabrikam

        ![Azure VM 및 AD 포리스트 템플릿 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. 클릭 **구매**
    1. 배포를 모니터링 하 고 완료 될 때까지 기다립니다.
    1. 리소스가 올바른 리소스 그룹에서 만들어지는 확인 `OnPremADVRG`합니다.

## <a name="configure-users-and-groups-for-cluster-access"></a>사용자 및 그룹 클러스터 액세스에 대 한 구성

개요: 이 섹션에서는이 가이드의 끝에서 HDInsight 클러스터에 액세스할 수 있는 사용자를 만듭니다.

1. 원격 데스크톱을 사용 하 여 도메인 컨트롤러에 연결 합니다.
    1. 도메인 컨트롤러를 호출 하는 VM 시작 부분에서 언급 한 서식 파일을 사용 하는 경우 **adVM** 에 `OnPremADVRG` 리소스 그룹입니다.
    1. Azure portal로 이동 > **리소스 그룹** > **OnPremADVRG** > **adVM** > **Connect**.
    1. 클릭 합니다 **RDP** 탭을 클릭 한 다음 **RDP 파일 다운로드**합니다.
    1. 컴퓨터에 파일을 저장 하 고 엽니다.
    1. 사용 하 여 자격 증명을 묻는 메시지가 나타나면 `HDIFabrikam\HDIFabrikamAdmin` 사용자 이름으로 관리자 계정에 대해 선택한 암호를 입력 합니다.

1. 도메인 컨트롤러 VM에 원격 데스크톱 세션 열리면 시작할 **Active Directory Users and Computers** 에서 합니다 **서버 관리자** 대시보드. 클릭 **도구가** 오른쪽 위에 있는 차례로 **Active Directory Users and Computers** 드롭다운 목록에서.

    ![서버 관리자 열기 Active Directory 관리](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 두 명의 새 사용자를 만듭니다 **HDIAdmin**하십시오 **HDIUser**합니다. 이러한 두 명의 사용자가 HDInsight 클러스터에 로그인 됩니다.

    1. 에 **Active Directory 사용자 및 컴퓨터** 화면에서 **동작** > **새로 만들기** > **사용자**합니다.

        ![새 Active Directory 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. 에 **새 개체-사용자** 화면에서 입력 `HDIUser` 으로 **사용자 로그온 이름** 클릭 **다음**합니다.

        ![첫 번째 관리 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. 나타나는 팝업에서 새 계정에 대 한 원하는 암호를 입력 합니다. 확인란을 선택 한다는 **암호 사용 기간 제한 없음**합니다. HDIClick **확인**합니다.
    1. 클릭 **완료** 새 계정을 만드십시오.
    1. 다른 사용자를 만듭니다 `HDIAdmin`합니다.

        ![두 번째 관리 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. 에 **Active Directory 사용자 및 컴퓨터** 화면에서 **동작** > **새로 만들기** > **그룹**합니다. 만들 `HDIUserGroup` 새 그룹으로 합니다.

    ![새 Active Directory 그룹 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![새 그룹 2를 만들려면](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. 추가 합니다 **HDIUser** 이전 단계에서 만든 합니다 **HDIUserGroup** 멤버로 합니다.

    1. 마우스 오른쪽 단추로 클릭 합니다 **HDIUserGroup** 누릅니다 **속성**합니다.
    1. 로 이동 **멤버** 탭을 클릭 **추가**합니다.
    1. Enter `HDIUser` 입력란에 **선택할 개체 이름을 입력 하십시오** 클릭 **확인**합니다.
    1. 다른 계정에 대해 이전 단계를 반복 합니다. `HDIAdmin`

        ![그룹에 멤버 추가](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

이제 두 명의 사용자와 함께 Active Directory 환경의 및 만든 사용자 그룹을 HDInsight 클러스터에 액세스 하는 것에 대 한 합니다.

이러한 사용자를 Azure AD와 동기화 됩니다.

### <a name="create-a-new-azure-active-directory"></a>새 Azure Active Directory 만들기

1. Azure 포털에 로그인합니다.
1. 클릭 **리소스 만들기** 유형과 **directory**합니다. 선택 **Azure Active Directory** > **만들**합니다.
1. 입력 **HDIFabrikam** 아래에서 **조직 이름**합니다.
1. 입력 **HDIFabrikamoutlook** 아래에서 **초기 도메인 이름**합니다.
1. **만들기**를 클릭합니다.
1. Azure portal의 왼쪽에서 클릭 **Azure Active Directory**합니다.
1. 필요한 경우 클릭 **디렉터리 전환** 사용자가 만든 새 디렉터리를 변경 하려면 **HDIFabrikamoutlook**합니다.
1. 아래 **관리** 클릭 **사용자 지정 도메인 이름** > **사용자 지정 도메인 추가**합니다.
1. 입력 **HDIFabrikam.com** 아래에서 **사용자 지정 도메인 이름** 누릅니다 **도메인 추가**합니다.

![새 azure active directory 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![새 사용자 지정 도메인 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Azure AD 테 넌 트를 구성 합니다.

개요: 온-프레미스에서 사용자 및 그룹을 동기화 할 수 있도록 Azure AD 테 넌 트 구성 이제 클라우드에서 AD입니다.

1. AD 테 넌 트 관리자를 만듭니다.
    1. Azure portal에 로그인 하 고 Azure AD 테 넌 트 선택 **HDIFabrikam**
    1. 선택 **사용자가** 아래에서 **관리** 차례로 **새 사용자**합니다.
    1. 새 사용자에 대 한 다음 세부 정보를 입력 합니다.

        * 이름: fabrikamazureadmin
        * 사용자 이름: fabrikamazureadmin@hdifabrikam.com
        * 암호: 보안 암호를 원하는

    1. 클릭 합니다 **그룹** 섹션을 검색 한 **AAD DC Administrators**, 클릭 **선택**합니다.

        ![그룹](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. 클릭 합니다 **디렉터리 역할** 선택한 섹션 **전역 관리자** 오른쪽에서입니다. **Ok**를 클릭합니다.

        ![디렉터리 역할](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. 사용자에 대 한 암호를 입력 합니다. **만들기**를 클릭합니다.

1. 새로 만든된 사용자에 대 한 암호를 변경 하려는 경우 <fabrikamazureadmin@hdifabrikam.com>합니다. Azure portal 사용 하 여 id 및 다음은 암호를 변경 하 라는 메시지가 표시에 로그인 합니다.

## <a name="sync-on-premises-users-to-azure-ad"></a>동기화 온-프레미스 사용자를 Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>다운로드 및 설치 Microsoft Azure Active Directory 연결

1. [Azure AD Connect 다운로드](https://www.microsoft.com/download/details.aspx?id=47594).

1. 설치 Microsoft Azure Active Directory 도메인 컨트롤러에 연결 합니다.
    1. 이전 단계에서 다운로드 한 실행 파일을 열고 사용 조건에 동의 합니다. **계속**을 클릭합니다.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. 클릭 **기본 설정 사용** 설치를 완료 합니다.

        ![기본 설정 사용](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>온-프레미스 도메인 컨트롤러를 사용 하 여 동기화 구성

1. 에 **Azure AD에 연결** 화면에서 Azure AD의 사용자 이름 및 전역 관리자의 암호를 입력 합니다. 클릭 **다음**합니다. 이 사용자 이름 `fabrikamazureadmin@hdifabrikam.com` AD 테 넌 트를 구성할 때 만든 합니다.
    ![Azure AD에 연결](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. 에 **Active Directory Domain Services에 연결** 화면에서 엔터프라이즈 관리자 계정의 사용자 이름과 암호를 입력 합니다. 클릭 **다음**합니다. 이 사용자 이름 `HDIFabrikam\HDIFabrikamAdmin` 및 이전에 만든 일치 하는 암호입니다.

   ![Active Directory Domain Services에 연결](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. 에 **Azure AD 로그인 구성을** 페이지에서 클릭 **다음**합니다.
    ![Azure AD 로그인 구성](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. 화면 구성 준비 완료를 클릭 **설치**합니다.
    ![설치](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. 경우는 **구성을 완료할** 화면이 표시 되 면 클릭 **종료**합니다.
    ![구성 완료](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. 동기화 완료 된 후에 IAAS Active Directory에서 만든 사용자는 Azure Active Directory와 동기화 된 경우를 확인 합니다.
    1. Azure 포털에 로그인합니다.
    1. 선택 **Azure Active Directory** > **HDIFabrikam** > **사용자**합니다.

### <a name="create-an-user-assigned-managed-identity"></a>사용자 할당 관리 되는 id 만들기

Azure Active Directory Domain Services (Azure AD DS)를 구성 하는 데 사용할 수 있는 사용자 할당 관리 되는 id를 만듭니다. 사용자 할당 관리 id를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Create, list, delete 또는 Azure portal을 사용 하 여 사용자 할당 관리 되는 id에 역할 할당](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)합니다.

1. Azure 포털에 로그인합니다.
1. 클릭 **리소스 만들기** 유형과 **관리 id**합니다. 선택 **사용자 할당 관리 되는 Id** > **만들**합니다.
1. 입력 **HDIFabrikamManagedIdentity** 으로 **리소스 이름**합니다.
1. 구독을 선택합니다.
1. 아래 **리소스 그룹** 클릭 **새로 만들기** 입력 **HDIFabrikam CentralUS**합니다.
1. 선택 **미국 중부** 아래에서 **위치**합니다.
1. **만들기**를 클릭합니다.

![새 사용자 할당 관리 되는 id 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 활성화

자세한 내용은 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)를 참조하세요.

1. Azure AD DS 호스트에 가상 네트워크를 만듭니다. 다음 powershell 코드를 실행 합니다.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure 포털에 로그인합니다.
1. 클릭 **리소스 만들기**를 입력 **Domain services** 선택한 **Azure AD Domain Services**합니다.
1. 에 **기본 사항** 화면에는 다음 단계를 완료 합니다.
    1. 아래 **디렉터리가** 이 자습서에서는 만든 Azure Active Directory 선택 **HDIFabrikam**.
    1. 입력 한 **DNS 도메인 이름** 의 **HDIFabrikam.com**합니다.
    1. 구독을 선택합니다.
    1. 리소스 그룹을 지정 **HDIFabrikam CentralUS** 하며 **위치** 의 **중미**합니다.

        ![azure ad ds에 대 한 기본 정보](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. 에 **네트워크** 전체 화면에서 네트워크를 선택 (**HDIFabrikam VNET**) 및 서브넷 (**AADDS 서브넷**) 앞의 powershell 스크립트를 사용 하 여 만든 합니다. 사용할 수 있습니다 합니다 **새로 만들기** 이제 가상 네트워크를 만드는 옵션입니다.

    ![네트워크 선택](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. 에 **관리자 그룹** 화면에서 그룹 이라는 알림이 표시 됩니다 **AAD DC Administrators** 이 그룹 관리를 이미 만들었습니다. 이 그룹의 멤버 자격이 필요에 따라 수정할 수는 있지만이 자습서의 단계에 필요 하지 않습니다. **확인**을 클릭합니다.

    ![관리자 그룹 보기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. 에 **동기화** 화면에서 선택 하 여 전체 동기화를 사용 하도록 설정 **모든** 을 클릭 한 다음 **확인**합니다.

    ![동기화를 사용 하도록 설정](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. 에 **요약** 화면에서 Azure AD-DS에 대 한 세부 정보를 확인 하 고 클릭 **확인**합니다.

    ![세부 정보 확인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Azure AD-DS를 사용하도록 설정하면 로컬 DNS(도메인 이름 서비스) 서버가 AD VM(Virtual Machine)에서 실행됩니다.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크를 구성 합니다.

이 섹션의 단계를 Azure AD DS 가상 네트워크를 구성할 수 있습니다 (**HDIFabrikam AADDSVNET**) 사용자 지정 DNS 서버를 사용 하도록 합니다.

1. 사용자 지정 DNS 서버의 IP 주소를 찾습니다. 클릭 합니다 **HDIFabrikam.com** AD DS 리소스 클릭 **속성** 아래에서 **관리**   아래에 나열 된 IP 주소를 확인 하 고 **IP 가상 네트워크의 주소를**입니다.

    ![Azure AD DS에 대 한 사용자 지정 DNS IP 주소를 찾습니다](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. 구성할 **HDIFabrikam AADDSVNET** 사용자 지정 ip `10.0.0.4` 고 `10.0.0.5`입니다.

    1. 선택 **DNS 서버** 아래의 합니다 **설정** 범주입니다. 옆에 라디오 단추를 클릭 **사용자 지정**텍스트 상자 아래에 첫 번째 IP 주소 (10.0.0.4)를 입력 하 고 클릭 **저장**합니다.
    1. 동일한 단계를 사용 하 여 추가 IP 주소 (10.0.0.5)를 추가 합니다.

1. 이 시나리오에서는 Azure AD DS는 아래 이미지에 표시 된 대로 AADDS VNet에서 IP 주소 10.0.0.4 및 10.0.0.5를 설정 하는 동일한 IP 주소를 사용 하도록 구성 되었습니다.

    ![사용자 지정 dns 서버 보기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>LDAP 트래픽 보안

읽기 및 쓰기 Active Directory lightweight Directory Access 프로토콜 (LDAP) 됩니다. 기밀 및 보호 Secure Sockets Layer (SSL)를 사용 하 여 트래픽 LDAP을 확인 수 / 전송 계층 보안 (TLS) 기술입니다. 올바르게 서식이 지정 된 인증서를 설치 하 여 SSL (LDAPS)를 통해 LDAP를 설정할 수 있습니다.

보안 LDAP에 대 한 자세한 내용은 참조 하세요. [구성 보안 LDAP (LDAPS) Azure AD Domain Services에 대 한 관리 되는 도메인](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)합니다.

이 섹션에서는 자체 서명 된 인증서, 인증서를 다운로드 합니다 만들고 보안 LDAP (LDAPS) 구성에 대 한 합니다 **hdifabrikam** Azure AD DS 관리 되는 도메인입니다.

다음 스크립트는 hdifabrikam에 대 한 인증서를 만듭니다. 인증서는 "LocalMachine" 경로 아래 저장 됩니다.

> [!Note] 
> 모든 유틸리티 또는 유효한 PKCS 만드는 응용 프로그램을 \#10 요청 SSL 인증서 요청을 만드는 데 사용할 수 있습니다.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

인증서는 컴퓨터에 설치 되어 있는지 확인\'s 개인 저장소. 다음 단계를 완료합니다.

1. Microsoft Management Console (MMC)를 시작 합니다.
1. 로컬 컴퓨터의 인증서를 관리 하는 인증서 스냅인을 추가 합니다.
1. 확장 **인증서 (로컬 컴퓨터)** 를 확장 하 고 **개인**를 차례로 확장 **인증서**합니다. 새 인증서를 개인 저장소에 있어야 합니다. 이 인증서는 정규화 된 호스트 이름으로 발급 됩니다.

    ![인증서 만들기를 확인 합니다.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. 오른쪽 창에서 이전 단계에서 만든 인증서를 마우스 오른쪽 단추로 가리킵니다 **모든 작업**를 클릭 하 고 **내보내기**합니다.

1. 에 **개인 키 내보내기** 페이지에서 클릭 **예, 개인 키 내보내기**합니다. 개인 키가 키를 가져와야 하는 컴퓨터에서 읽을 암호화 된 메시지에 필요 합니다.

    ![프라이빗 키 내보내기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. 에 **내보내기 파일 형식** 페이지에서 기본 설정을 유지 하 고, 클릭 **다음**합니다. 
1. 에 **암호** 페이지에서 개인 키를 선택에 대 한 암호를 입력 **TripleDES-SHA1** 에 대 한 **암호화** 를 클릭 하 고 **다음**.
1. 에 **내보낼 파일** 페이지에서 내보낸된 인증서 파일의 이름과 경로 입력 하 고, 클릭 **다음**합니다.
1. .Pfx 확장 되도록 파일 이름에,이 파일은 보안 연결을 설정 하려면 Azure portal에서 구성 합니다.
1. 보안 LDAP (LDAPS) Azure AD Domain Services 관리 되는 도메인을 사용 하도록 설정 합니다.
    1. 도메인 선택 **HDIFabrikam.com** Azure portal에서 합니다.
    1. 클릭 **보안 LDAP** 아래에서 **관리**합니다.
    1. 에 **보안 LDAP** 화면에서 **사용 하도록 설정** 아래에 있는 **보안 LDAP**합니다.
    1. 컴퓨터에서 내보낸.pfx 인증서 파일을 찾습니다.
    1. 인증서 암호를 입력 합니다.

    ![보안 ldap를 사용 하도록 설정](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. 보안 LDAP를 사용 하도록 설정한 했으므로 포트 636을 사용 하 여 연결할 수는 있는지 확인 합니다.
    1. 네트워크 보안 그룹을 클릭 **AADDS-HDIFabrikam.com-NSG** 에 **HDIFabrikam CentralUS** 리소스 그룹입니다.
    1. 아래 **설정을** 클릭 **인바운드 보안 규칙** > **추가**합니다.
    1. 에 **인바운드 보안 규칙 추가** 화면에서 다음 속성을 입력 하 고 클릭 **추가**:

        | 자산 | Value |
        |---|---|
        | 원본 | 모두 |
        | 원본 포트 범위 | * |
        | 대상 | 모두 |
        | 대상 포트 범위 | 636 |
        | Protocol | 모두 |
        | 액션(Action) | 허용 |
        | 우선 순위 | <Desired Number> |
        | 이름 | Port_LDAP_636 |

    ![인바운드 보안 규칙](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` 관리 사용자 할당 id, HDInsight 도메인 서비스 참가자 역할은 읽기, 만들기, 수정 및 도메인 서비스 작업을 삭제 하려면이 Id를 사용 하는 관리 되는 id에 사용 하도록 설정 됩니다.

    ![관리 되는 id를 할당 하는 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>엔터프라이즈 보안 패키지를 만드는 HDInsight 클러스터에 사용 하도록 설정

이 단계는 다음과 같은 필수를 필요합니다.

1. 새 리소스 그룹을 만듭니다 `HDIFabrikam-WestUS` 위치에 `West US`입니다.
1. 만들 가상 네트워크 ESP를 호스트 하는 HDInsight 클러스터를 사용 하도록 설정 합니다.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. AADDS 호스팅하는 가상 네트워크 간의 피어 관계를 만듭니다 (`HDIFabrikam-AADDSVNET`) 및 ESP를 호스트 하는 가상 네트워크에 HDInsight 클러스터 사용 하도록 설정 (`HDIFabrikam-HDIVNet `). 다음 powershell 코드를 사용 하 여 이러한 두 가상 네트워크를 피어 링 합니다.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 새 Azure Data Lake 저장소 Gen2 계정을 만들 **Hdigen2store**되는 관리 되는 사용자 id를 사용 하 여 구성 **HDIFabrikamManagedIdentity**합니다. 사용자를 사용 하는 Data Lake 저장소 Gen2 계정을 만드는 방법은 관리 되는 id를 참조 하세요 [Azure HDInsight를 사용 하 여 사용 하 여 Azure Data Lake 저장소 Gen2 클러스터](../hdinsight-hadoop-use-data-lake-storage-gen2.md)합니다.

1. 에 사용자 지정 DNS를 설치 합니다 **HDIFabrikam AADDSVNET** 가상 네트워크.
    1. Azure portal로 이동 > **리소스 그룹** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **DNS 서버**합니다.
    1. 선택 **사용자 지정** enter `10.0.0.4` 고 `10.0.0.5`입니다.
    1. **저장**을 클릭합니다.

        ![사용자 지정 dns 설정을 저장 합니다.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. 새 ESP 지원 HDInsight Spark 클러스터를 만듭니다.
    1. 클릭 **사용자 지정 (크기, 설정, 앱)** 합니다.
    2. 섹션 1 원하는 세부 정보를 입력 **기본 사항**합니다. 있는지 확인 합니다 **클러스터 유형** 됩니다 **Spark 2.3 (HDI 3.6)** 및 **리소스 그룹** 는 **HDIFabrikam CentralUS**

    1. 섹션 2 **보안 + 네트워킹**, 다음 단계를 완료 합니다.
        1. 클릭 **활성화** 아래에서 **엔터프라이즈 보안 패키지**합니다.
        1. 클릭 **클러스터 관리자** 선택 합니다 **HDIAdmin** 온-프레미스 관리 사용자로 이전에 만든 계정. **선택**을 클릭합니다.

        1. 클릭 **클러스터 액세스 그룹** 선택한 후 **HDIUserGroup**합니다. 나중에이 그룹에 추가 하는 모든 사용자는 HDInsight 클러스터에 액세스할 수 됩니다.

            ![클러스터 액세스 그룹을 선택 합니다.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. 클러스터 구성의 다른 단계를 완료 하 고 세부 정보를 확인 합니다 **클러스터 요약**합니다. **만들기**를 클릭합니다.

1. 새로 만든된 클러스터의 Ambari ui에 로그인 `https://CLUSTERNAME.azurehdinsight.net` 관리자 사용자 이름을 사용 하 여 `hdiadmin@hdifabrikam.com` 및 암호입니다.

    ![Ambari에 로그인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. 클릭 **역할** 클러스터 대시보드에서.
1. 에 **역할** 페이지에서 그룹을 입력 합니다 **hdiusergroup** 을 할당할 합니다 **클러스터 관리자** 역할 **이러한 역할을 할당**합니다.

    ![hdiusergroup에 클러스터 관리자 역할 할당](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. 하 여 SSH 클라이언트를 열고 사용 하 여 클러스터에 로그인 합니다 **hdiuser** 온-프레미스 Active Directory에서 이전에 생성 합니다.

    ![SSH 사용 하 여 클러스터에 로그인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

이 계정으로 로그인 할 수 있다면 다음 구성한 ESP 클러스터 올바르게 온-프레미스 active directory와 동기화 합니다.

## <a name="next-steps"></a>다음 단계

* [Enterprise Security Package를 사용하여 Apache Hadoop 보안 소개](apache-domain-joined-introduction.md)
