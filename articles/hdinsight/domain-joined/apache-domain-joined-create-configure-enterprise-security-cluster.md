---
title: 엔터프라이즈 보안 패키지 클러스터 만들기, 구성 - Azure
description: Azure HDInsight에서 엔터프라이즈 보안 패키지 클러스터를 만들고 구성하는 방법에 대해 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: fb3484d013314897ea2e9157b642d8f2b85dcd60
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437643"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Azure HDInsight에서 엔터프라이즈 보안 패키지 클러스터 생성 및 구성

Azure HDInsight용 엔터프라이즈 보안 패키지(ESP)를 사용하면 Azure의 아파치 하두롭 클러스터에 대한 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 액세스 제어에 액세스할 수 있습니다. HDInsight ESP 클러스터를 사용하면 엄격한 회사 보안 정책을 준수하는 조직이 중요한 데이터를 안전하게 처리할 수 있습니다.

이 가이드에서는 ESP 지원 Azure HDInsight 클러스터를 만드는 방법을 보여 주며, 이 가이드에서는 ESP 지원 Azure HDInsight 클러스터를 만드는 방법을 보여 주시면 됩니다. 또한 Active Directory 및 도메인 이름 시스템(DNS)이 활성화된 Windows IaaS VM을 만드는 방법도 보여 주며, 이 방법을 보여 주시면 됩니다. 온-프레미스 사용자가 ESP 지원 HDInsight 클러스터에 로그인할 수 있도록 필요한 리소스를 구성하려면 이 가이드를 사용합니다.

생성한 서버는 *실제* 온-프레미스 환경을 대체하는 역할을 합니다. 설치 및 구성 단계에 사용합니다. 나중에 사용자 고유의 환경에서 단계를 반복합니다.

또한 이 가이드에서는 Azure Active Directory(Azure AD)와 암호 해시 동기화를 사용하여 하이브리드 ID 환경을 만드는 데도 도움이 됩니다. 이 가이드는 [HDInsight에서 ESP 사용을](apache-domain-joined-architecture.md)보완합니다.

사용자 고유의 환경에서 이 프로세스를 사용하기 전에 다음을 수행하십시오.

* Active 디렉토리 및 DNS를 설정합니다.
* Azure AD를 사용하도록 설정합니다.
* 온-프레미스 사용자 계정을 Azure AD에 동기화합니다.

![Azure AD 아키텍처 다이어그램](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>온-프레미스 환경 만들기

이 섹션에서는 Azure Quickstart 배포 템플릿을 사용하여 새 VM을 만들고, DNS를 구성하고, 새 Active Directory 포리스트를 추가합니다.

1. 빠른 시작 배포 템플릿으로 이동하여 [새 Active Directory 포리스트를 사용하여 Azure VM만들기.](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. **Azure에 배포를**선택합니다.
1. Azure 구독에 로그인합니다.
1. 새 **AD 포리스트가 있는 Azure VM 만들기** 페이지에서 다음 정보를 제공합니다.

    |속성 | 값 |
    |---|---|
    |Subscription|리소스를 배포할 구독을 선택합니다.|
    |Resource group|새 수 **만들기를**선택하고 이름을 입력합니다.`OnPremADVRG`|
    |위치|위치를 선택합니다.|
    |관리자 사용자 이름|`HDIFabrikamAdmin`|
    |관리자 암호|암호를 입력합니다.|
    |도메인 이름|`HDIFabrikam.com`|
    |Dns 접두사|`hdifabrikam`|

    나머지 기본값을 그대로 둡니다.

    ![새 Azure AD 포리스트를 사용하여 Azure VM 만들기템플릿](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. 이용 **약관을**검토한 다음 **위에 명시된 이용 약관에 동의합니다.**
1. **구입을**선택하고 배포를 모니터링하고 완료될 때까지 기다립니다. 배포를 완료하는 데 약 30분이 걸립니다.

## <a name="configure-users-and-groups-for-cluster-access"></a>클러스터 액세스를 위한 사용자 및 그룹 구성

이 섹션에서는 이 가이드의 끝부분까지 HDInsight 클러스터에 액세스할 수 있는 사용자를 만듭니다.

1. 원격 데스크톱을 사용하여 도메인 컨트롤러에 연결합니다.
    1. Azure 포털에서 리소스 **그룹** > **OnPremADVRG** > **adVM** > **연결로**이동합니다.
    1. IP **주소** 드롭다운 목록에서 공용 IP 주소를 선택합니다.
    1. **RDP 파일 다운로드를**선택한 다음 파일을 엽니다.
    1. 사용자 `HDIFabrikam\HDIFabrikamAdmin` 이름으로 사용합니다.
    1. 관리자 계정에 대해 선택한 암호를 입력합니다.
    1. **확인**을 선택합니다.

1. 도메인 컨트롤러 **서버 관리자** 대시보드에서 **도구** > **Active Directory 사용자 및 컴퓨터로**이동합니다.

    ![서버 관리자 대시보드에서 활성 디렉터리 관리를 엽니다.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 두 명의 새로운 사용자 만들기: **HDIAdmin** 및 **HDIUser**. 이 두 사용자는 HDInsight 클러스터에 로그인합니다.

    1. Active **Directory 사용자 및 컴퓨터** 페이지에서 `HDIFabrikam.com`을 마우스 오른쪽 단추로 클릭한 다음 **새** > **사용자로**이동합니다.

        ![새 Active Directory 사용자 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. 새 **개체 - 사용자** 페이지에서 `HDIUser` **이름** 및 **사용자 로그온 이름을**입력합니다. 다른 필드는 자동 채워집니다. 그런 다음 을 **선택합니다.**

        ![첫 번째 관리자 사용자 개체 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 표시되는 팝업 창에서 새 계정의 암호를 입력합니다. **암호가 만료되지 않는**지 선택한 다음 팝업 메시지에서 **확인을** 선택합니다.
    1. **다음**을 선택한 다음 **완료를** 클릭하여 새 계정을 만듭니다.
    1. 위의 단계를 반복하여 사용자를 `HDIAdmin`만듭니다.

        ![두 번째 관리자 사용자 개체 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 글로벌 보안 그룹을 만듭니다.

    1. **활성 디렉터리 사용자 및 컴퓨터에서** `HDIFabrikam.com`를 마우스 오른쪽 단추로 클릭한 다음 **새** > **그룹으로**이동합니다.

    1. 그룹 `HDIUserGroup` **이름** 텍스트 상자에 입력합니다.

    1. **확인**을 선택합니다.

    ![새 Active Directory 그룹 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![새 개체 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. **HDIUserGroup에 멤버를 추가합니다.**

    1. **HDIUser를** 마우스 오른쪽 버튼으로 클릭하고 **그룹에 추가를 선택합니다...**.
    1. 텍스트 상자를 **선택하려면 개체 이름을 입력하려면** 을 입력합니다. `HDIUserGroup` 그런 다음 팝업에서 **확인을**선택하고 다시 **확인을** 선택합니다.
    1. **HDIAdmin** 계정에 대한 이전 단계를 반복합니다.

        ![그룹 HDIUser그룹에 멤버 HDIUser 추가](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

이제 Active 디렉터리 환경을 만들었습니다. HDInsight 클러스터에 액세스할 수 있는 두 명의 사용자와 사용자 그룹을 추가했습니다.

사용자는 Azure AD와 동기화됩니다.

### <a name="create-an-azure-ad-directory"></a>Azure AD 디렉터리 만들기

1. Azure Portal에 로그인합니다.
1. **리소스 만들기를** 선택하고 `directory`을 입력합니다. **Azure 활성 디렉터리** > **만들기를 선택합니다.**
1. **조직 이름**에서 `HDIFabrikam`를 입력합니다.
1. **초기 도메인 이름** `HDIFabrikamoutlook`에서 를 입력합니다.
1. **만들기**를 선택합니다.

    ![Azure AD 디렉터리 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>사용자 지정 도메인 만들기

1. 새 **Azure Active Directory에서** **관리**에서 사용자 지정 **도메인 이름을**선택합니다.
1. **+ 사용자 지정 도메인 추가를**선택합니다.
1. 사용자 지정 도메인 `HDIFabrikam.com` **이름에서**을 입력한 다음 도메인 **추가를**선택합니다.
1. 그런 다음 [도메인 등록 기관에 DNS 정보 추가를 완료합니다.](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)

![사용자 지정 도메인 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>그룹 만들기

1. 새 **Azure Active Directory에서** **관리**에서 **그룹을**선택합니다.
1. **+ 새 그룹을**선택합니다.
1. 그룹 **이름** 텍스트 상자에 `AAD DC Administrators`를 입력합니다.
1. **만들기**를 선택합니다.

## <a name="configure-your-azure-ad-tenant"></a>Azure AD 테넌트 구성

이제 온-프레미스 Active Directory 인스턴스에서 클라우드로 사용자 및 그룹을 동기화할 수 있도록 Azure AD 테넌트를 구성합니다.

Active Directory 테넌트 관리자를 만듭니다.

1. Azure 포털에 로그인하고 Azure AD 테넌트 인 **HDIFabrikam을**선택합니다.

1.  > **사용자** **관리새** > **사용자로**이동 .

1. 새 사용자에 대한 다음 세부 정보를 입력합니다.

    **ID**

    |속성 |Description |
    |---|---|
    |사용자 이름|텍스트 상자에 `fabrikamazureadmin`를 입력합니다. 도메인 이름 드롭다운 목록에서`hdifabrikam.com`|
    |속성| `fabrikamazureadmin`를 입력합니다.|

    **암호**
    1. **암호를 만들수 있도록 선택합니다.**
    1. 선택한 보안 암호를 입력합니다.

    **그룹 및 역할**
    1. **선택된 그룹 0을 선택합니다.**
    1. **AAD DC 관리자를**선택한 다음 **을 선택합니다.**

    ![Azure AD 그룹 대화 상자](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. **사용자**를 선택합니다.
    1. **전역 관리자를**선택한 다음 **을 선택합니다.**

    ![Azure AD 역할 대화 상자](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. **만들기**를 선택합니다.

1. 그런 다음 새 사용자가 Azure 포털에 로그인하여 암호를 변경하라는 메시지가 표시됩니다. Microsoft Azure 활성 디렉터리 연결을 구성하기 전에 이 작업을 수행해야 합니다.

## <a name="sync-on-premises-users-to-azure-ad"></a>온-프레미스 사용자를 Azure AD에 동기화

### <a name="configure-microsoft-azure-active-directory-connect"></a>마이크로소프트 Azure 활성 디렉터리 연결 구성

1. 도메인 컨트롤러에서 [Microsoft Azure 활성 디렉터리 연결](https://www.microsoft.com/download/details.aspx?id=47594)을 다운로드합니다.

1. 다운로드한 실행 파일을 열고 라이센스 조건에 동의합니다. **계속**을 선택합니다.

1. **익스프레스 설정 사용을**선택합니다.

1. Azure **AD에 연결** 페이지에서 Azure AD에 대한 전역 관리자의 사용자 이름과 암호를 입력합니다. Active Directory `fabrikamazureadmin@hdifabrikam.com` 테넌트를 구성할 때 만든 사용자 이름을 사용합니다. 그런 다음 을 **선택합니다.**

    !["Azure AD에 연결" 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. 활성 **디렉터리 도메인 서비스 연결** 페이지에서 엔터프라이즈 관리자 계정의 사용자 이름과 암호를 입력합니다. 이전에 만든 `HDIFabrikam\HDIFabrikamAdmin` 사용자 이름과 암호를 사용합니다. 그런 다음 을 **선택합니다.**

   !["Azure AD에 연결" 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Azure **AD 로그인 구성** 페이지에서 **다음**을 선택합니다.
   !["Azure AD 로그인 구성" 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. 구성 **준비** 페이지에서 **설치를**선택합니다.

   !["구성 준비" 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. **구성 완료** 페이지에서 **끝내기**를 선택합니다.
   !["구성 완료" 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 동기화가 완료되면 IaaS 디렉터리에서 만든 사용자가 Azure AD에 동기화되어 있는지 확인합니다.
   1. Azure Portal에 로그인합니다.
   1. **Azure Active 디렉터리** > **HDIFabrikam** > **사용자를**선택합니다.

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

Azure AD 도메인 서비스(Azure AD DS)를 구성하는 데 사용할 수 있는 사용자 할당된 관리 ID를 만듭니다. 자세한 내용은 [Azure 포털을 사용하여 사용자가 할당한 관리 ID에 역할 만들기, 목록, 삭제 또는 할당을](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)참조합니다.

1. Azure Portal에 로그인합니다.
1. **리소스 만들기를** 선택하고 `managed identity`을 입력합니다. **사용자 할당된 관리되는 ID** > **만들기 를 선택합니다.**
1. 리소스 **이름에**대해 `HDIFabrikamManagedIdentity`을 입력합니다.
1. 구독을 선택합니다.
1. 리소스 그룹에서 **새 만들기를** `HDIFabrikam-CentralUS`선택하고 을 **입력합니다.**
1. **위치**에서 **미국 중부를**선택합니다.
1. **만들기**를 선택합니다.

![새 사용자 할당관리 ID 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure AD DS 사용

Azure AD DS를 사용하도록 설정하려면 다음 단계를 따릅니다. 자세한 내용은 [Azure 포털을 사용하여 Azure AD DS 활성화를](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)참조하세요.

1. Azure AD DS를 호스트하는 가상 네트워크를 만듭니다. 다음 PowerShell 코드를 실행합니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure Portal에 로그인합니다.
1. **리소스 만들기를** `Domain services`선택하고 을 입력하고 **Azure AD 도메인 서비스** > **만들기를**선택합니다.
1. **기본** 사항 페이지에서:
    1. **디렉터리 이름에서**만든 Azure AD 디렉터리: **HDIFabrikam**을 선택합니다.
    1. **DNS 도메인 이름의**경우 *HDIFabrikam.com*를 입력합니다.
    1. 구독을 선택합니다.
    1. 리소스 그룹 **HDIFabrikam-CentralUS를**지정합니다. **위치의**경우 **미국 중부를**선택합니다.

        ![Azure AD DS 기본 세부 정보](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. **네트워크** 페이지에서 PowerShell 스크립트를 사용하여 만든**네트워크(HDIFabrikam-VNET)와****서브넷(AADDS-서브넷)을**선택합니다. 또는 **새 만들기를** 선택하여 지금 가상 네트워크를 만듭니다.

    !["가상 네트워크 만들기" 단계](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 관리자 **그룹** 페이지에 **AAD DC Administrators라는** 그룹이 이 그룹을 관리하기 위해 이미 만들어졌다는 알림이 표시됩니다. 원하는 경우 이 그룹의 구성원을 수정할 수 있지만 이 경우에는 변경할 필요가 없습니다. **확인**을 선택합니다.

    ![Azure AD 관리자 그룹 보기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. **동기화** 페이지에서 **모두** > **확인을**선택하여 완전한 동기화를 활성화합니다.

    ![Azure AD DS 동기화 사용](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. **요약** 페이지에서 Azure AD DS에 대한 세부 정보를 확인하고 **확인을**선택합니다.

    !["Azure AD 도메인 서비스 사용" 요약](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Azure AD DS를 사용하도록 설정하면 로컬 DNS 서버가 Azure AD VM에서 실행됩니다.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크 구성

다음 단계를 사용하여 Azure AD DS 가상**네트워크(HDIFabrikam-AADDSVNET)를**구성하여 사용자 지정 DNS 서버를 사용합니다.

1. 사용자 지정 DNS 서버의 IP 주소를 찾습니다.
    1. Azure `HDIFabrikam.com` AD DS 리소스를 선택합니다.
    1. **에서 관리**에서 **속성을**선택합니다.
    1. 가상 네트워크에서 IP **주소**에서 IP 주소를 찾습니다.

    ![Azure AD DS에 대한 사용자 지정 DNS IP 주소 찾기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. 사용자 지정 IP 주소를 10.0.0.4 및 10.0.0.5로 사용하도록 **HDIFabrikam-AADDSVNET을** 구성합니다.

    1. **설정에서** **DNS 서버를 선택합니다.**
    1. 사용자 지정 을 **선택합니다.**
    1. 텍스트 상자에 첫 번째 IP*주소(10.0.0.4)를*입력합니다.
    1. **저장**을 선택합니다.
    1. 다른 IP*주소(10.0.0.5)를*추가하는 단계를 반복합니다.

이 시나리오에서는 Azure AD DS를 사용하여 AZURE AD DS 가상 네트워크에서 동일한 IP 주소를 설정하여 IP 주소를 10.0.0.4 및 10.0.0.5로 설정하도록 구성했습니다.

![사용자 지정 DNS 서버 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP 트래픽 보안

LDAP(라이트급 디렉터리 액세스 프로토콜)는 Azure Active Directory에서 읽고 쓰는 데 사용됩니다. SSL(보안 소켓 계층) 또는 TLS(전송 계층 보안) 기술을 사용하여 LDAP 트래픽을 기밀로 유지및 보호할 수 있습니다. 적절한 형식의 인증서를 설치하여 LDAP(LDAPS)를 통해 LDAP를 활성화할 수 있습니다.

보안 LDAP에 대한 자세한 내용은 [Azure AD DS 관리 도메인에 대한 LDAPS 구성을](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)참조하십시오.

이 섹션에서는 자체 서명된 인증서를 만들고 인증서를 다운로드하고 **HDIFabrikam** Azure AD DS 관리 도메인에 대한 LDAPS를 구성합니다.

다음 스크립트는 **HDIFabrikam에**대한 인증서를 만듭니다. 인증서는 *LocalMachine* 경로에 저장됩니다.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> 유효한 공개 키 암호화 표준(PKCS) \#10 요청을 만드는 모든 유틸리티 또는 응용 프로그램을 사용하여 TLS/SSL 인증서 요청을 형성할 수 있습니다.

인증서가 컴퓨터의 **개인** 저장소에 설치되어 있는지 확인합니다.

1. 마이크로 소프트 관리 콘솔 (MMC)를 시작합니다.
1. 로컬 컴퓨터에서 **인증서를** 관리하는 인증서 스냅인을 추가합니다.
1. **인증서(로컬 컴퓨터)** > **개인** > **인증서 확장.** 새 인증서는 **개인** 저장소에 있어야 합니다. 이 인증서는 정규화된 호스트 이름으로 발급됩니다.

    ![로컬 인증서 생성 확인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 오른쪽 창에서 만든 인증서를 마우스 오른쪽 단추로 클릭합니다. 모든 **작업을**가리키고 **내보내기를**선택합니다.

1. 개인 **키 내보내기** 페이지에서 **예, 개인 키 내보내기를**선택합니다. 키를 가져올 컴퓨터에는 암호화된 메시지를 읽으려면 개인 키가 필요합니다.

    ![인증서 내보내기 마법사의 개인 키 내보내기 페이지](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. 파일 **형식 내보내기** 페이지에서 기본 설정을 그대로 두고 **다음**을 선택합니다.
1. **암호** 페이지에서 개인 키에 대한 암호를 입력합니다. **암호화의**경우 **트리플데스-SHA1을**선택합니다. 그런 다음 을 **선택합니다.**
1. **내보내기 파일** 페이지에서 내보낸 인증서 파일의 경로와 이름을 입력한 다음 **다음을**선택합니다. 파일 이름에는 .pfx 확장명이 있어야 합니다. 이 파일은 보안 연결을 설정하기 위해 Azure 포털에서 구성됩니다.
1. Azure AD DS 관리 도메인에 대해 LDAPS를 사용하도록 설정합니다.
    1. Azure 포털에서 도메인 `HDIFabrikam.com`을 선택합니다.
    1. **관리**에서 **LDAP 보안을**선택합니다.
    1. 보안 **LDAP** 페이지에서 **보안 LDAP**에서 **을 선택합니다.**
    1. 컴퓨터에서 내보낸 .pfx 인증서 파일을 찾아봅니다.
    1. 인증서 암호를 입력합니다.

    ![보안 LDAP를 사용하도록 설정](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. LDAPS를 사용하도록 설정되었으므로 포트 636을 활성화하여 연결할 수 있는지 확인합니다.
    1. **HDIFabrikam-CentralUS** 리소스 그룹에서 네트워크 보안 그룹 **AADDS-HDIFabrikam.com-NSG를**선택합니다.
    1. **설정에서** **인바운드 보안 규칙** > **추가를**선택합니다.
    1. **인바운드 보안 규칙 추가** 페이지에서 다음 속성을 입력하고 **다음 을 선택합니다.**

        | 속성 | 값 |
        |---|---|
        | 원본 | 모두 |
        | 원본 포트 범위 | * |
        | 대상 | 모두 |
        | 대상 포트 범위 | 636 |
        | 프로토콜 | 모두 |
        | 작업 | Allow |
        | 우선 순위 | \<원하는 번호> |
        | 속성 | Port_LDAP_636 |

    !["인바운드 보안 규칙 추가" 대화 상자](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikam관리아이덴티티는** 사용자가 할당한 관리형 ID입니다. HDInsight 도메인 서비스 기여자 역할은 이 ID가 도메인 서비스 작업을 읽고, 만들고, 수정하고, 삭제할 수 있도록 하는 관리되는 ID에 대해 활성화됩니다.

![사용자 할당 관리 ID 만들기](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP 지원 HDInsight 클러스터 만들기

이 단계에는 다음과 같은 필수 구성 조건이 필요합니다.

1. **미국 서부**위치에 새로운 리소스 그룹 *HDIFabrikam-WestUS를* 만듭니다.
1. ESP 지원 HDInsight 클러스터를 호스팅하는 가상 네트워크를 만듭니다.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure AD DS()를`HDIFabrikam-AADDSVNET`호스트하는 가상 네트워크와 ESP 지원 HDInsight 클러스터()를`HDIFabrikam-HDIVNet`호스트하는 가상 네트워크 간에 피어 관계를 만듭니다. 다음 PowerShell 코드를 사용하여 두 가상 네트워크를 피어로 만듭니다.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. **Hdigen2store라는**새 Azure 데이터 레이크 저장소 Gen2 계정을 만듭니다. 사용자 관리 ID **HDIFabrikamManagedIdIdIdId로**계정을 구성합니다. 자세한 내용은 [Azure HDInsight 클러스터를 사용하여 Azure 데이터 레이크 저장소 Gen2 사용을](../hdinsight-hadoop-use-data-lake-storage-gen2.md)참조하십시오.

1. **HDIFabrikam-AADDSVNET** 가상 네트워크에 사용자 정의 DNS를 설정합니다.
    1. Azure 포털 > **리소스 그룹** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS 서버로 이동합니다.**
    1. **사용자 지정을** 선택하고 *10.0.0.4* 및 *10.0.0.5를*입력합니다.
    1. **저장**을 선택합니다.

        ![가상 네트워크에 대한 사용자 지정 DNS 설정 저장](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 새로운 ESP 지원 HDInsight 스파크 클러스터를 만듭니다.
    1. **사용자 지정(크기, 설정, 앱)을**선택합니다.
    1. 기본 사항에 대한 세부 정보를 **입력합니다(섹션** 1). **클러스터 유형이** **스파크 2.3(HDI 3.6)인지**확인합니다. **리소스 그룹이** **HDIFabrikam-CentralUS인지**확인합니다.

    1. **보안 + 네트워킹(섹션** 2)의 경우 다음 세부 정보를 입력하십시오.
        * **엔터프라이즈 보안 패키지에서** **사용 옵션을**선택합니다.
        * **클러스터 관리자 사용자를** 선택하고 온-프레미스 관리자 사용자로 만든 **HDIAdmin** 계정을 선택합니다. **선택**을 클릭합니다.
        * **클러스터 액세스 그룹** > **HDIUserGroup을**선택합니다. 나중에 이 그룹에 추가하는 모든 사용자는 HDInsight 클러스터에 액세스할 수 있습니다.

            ![클러스터 액세스 그룹 HDIUser그룹 선택](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 클러스터 구성의 다른 단계를 완료하고 클러스터 **요약에**대한 세부 정보를 확인합니다. **만들기**를 선택합니다.

1. 에서 새로 만든 클러스터에 대해 Ambari `https://CLUSTERNAME.azurehdinsight.net`UI에 로그인합니다. 관리자 사용자 이름과 `hdiadmin@hdifabrikam.com` 암호를 사용합니다.

    ![아파치 암바리 UI 로그인 창](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 클러스터 대시보드에서 **역할**을 선택합니다.
1. **역할** 페이지에서 **클러스터 관리자** 역할 옆에 있는 이러한 에 **역할 할당**아래에 *hdiuser group*그룹을 입력합니다. 

    ![클러스터 관리자 역할을 hdiuser 그룹에 할당](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 보안 셸(SSH) 클라이언트를 열고 클러스터에 로그인합니다. 온-프레미스 Active Directory 인스턴스에서 만든 **hdiuser를** 사용합니다.

    ![SSH 클라이언트를 사용하여 클러스터에 로그인](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

이 계정으로 로그인할 수 있는 경우 온-프레미스 Active Directory 인스턴스와 동기화하도록 ESP 클러스터를 올바르게 구성했습니다.

## <a name="next-steps"></a>다음 단계

[ESP를 통해 아파치 하두프 보안 소개를](hdinsight-security-overview.md)읽어보십시오.
