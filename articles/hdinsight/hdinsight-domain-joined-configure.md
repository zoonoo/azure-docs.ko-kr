---
title: "도메인 가입 HDInsight 클러스터 구성 | Microsoft 문서"
description: "도메인에 가입된 HDInsight 클러스터를 설치 및 구성하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 424ee513afce6ab689c8804594754b1b49234754
ms.lasthandoff: 03/25/2017


---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>도메인에 가입된 HDInsight 클러스터 구성(미리 보기)
Azure AD(Azure Active Directory) 및 [Apache Ranger](http://hortonworks.com/apache/ranger/)로 Azure HDInsight 클러스터를 설치하여 강력한 인증 및 다양한 RBAC(역할 기반 액세스 제어) 정책을 활용하는 방법을 알아봅니다.  도메인에 가입된 HDInsight는 Linux 기반 클러스터에만 구성할 수 있습니다. 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 소개](hdinsight-domain-joined-introduction.md)를 참조하세요.

이 문서는 다음 시리즈의 첫 번째 자습서입니다.

* Apache Ranger를 사용하도록 설정된 Azure AD에 연결된(Azure Directory Domain Services 기능을 통해) HDInsight 클러스터를 만듭니다.
* Apache Ranger를 통해 Hive 정책을 생성 및 적용하고, 사용자(예: 데이터 과학자)가 Excel, Tableau 등의 ODBC 기반 도구를 사용하여 Hive에 연결할 수 있도록 허용합니다. Microsoft는 조만간 HBase, Spark, Storm 등의 다른 워크로드를 도메인에 가입된 HDInsight에 추가하기 위해 준비 중입니다.

최종 토폴로지의 예는 다음과 같습니다.

![도메인에 가입된 HDInsight 토폴로지](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

현재 Azure AD는 클래식 가상 네트워크(VNet)만 지원하고 Linux 기반 HDInsight 클러스터는 Azure Resource Manager 기반 VNet만 지원하므로 HDInsight Azure AD를 통합하려면 두 VNet이 있어야 하고 또 두 VNet을 피어링해야 합니다. 두 배포 모델의 비교 정보는 [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요. 두 VNet이 Azure AD DS와 동일한 지역에 있어야 합니다.

Azure 서비스 이름은 전역적으로 고유해야 합니다. 이 자습서에서는 다음 이름이 사용됩니다. Contoso는 가상의 이름입니다. 자습서를 공부할 때 *contoso*를 다른 이름으로 바꿔야 합니다. 

**이름:**

| 속성 | 값 |
| --- | --- |
| Azure AD VNet |contosoaadvnet |
| Azure AD VNet 리소스 그룹 |contosoaadrg |
| Azure AD 가상 컴퓨터(VM) |contosoaadadmin. 이 VM은 조직 단위를 구성하고 DNS 영역을 반전하는 데 사용됩니다. |
| Azure AD Directory |contosoaaddirectory |
| Azure AD 도메인 이름 |contoso(contoso.onmicrosoft.com) |
| HDInsight VNet |contosohdivnet |
| HDInsight VNet 리소스 그룹 |contosohdirg |
| HDInsight 클러스터 |contosohdicluster |

이 자습서에서는 도메인에 가입된 HDInsight 클러스터를 구성하는 단계를 설명합니다. 각 섹션에는 추가 배경 정보가 포함된 다른 문서의 링크가 있습니다.

## <a name="prerequisite"></a>필수 조건:
* [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) 및 [가격 책정](https://azure.microsoft.com/pricing/details/active-directory-ds/) 구조를 숙지합니다.
* 구독이 이 공개 미리 보기에 대한 허용 목록에 추가되었는지 확인합니다. 구독 ID로 hdipreview@microsoft.com에 전자 메일을 전송하여 추가할 수 있습니다.
* 도메인의 서명 기관에서 서명한 SSL 인증서가 필요합니다. 이 인증서는 보안 LDAP를 구성하는 데 필요합니다. 자체 서명된 인증서는 사용할 수 없습니다.

## <a name="procedures"></a>프로시저
1. Azure AD에 대한 Azure 클래식 VNet을 만듭니다.  
2. Azure AD 및 Azure AD DS를 만들고 구성합니다.
3. 조직 구성 단위를 만들기 위해 클래식 VNet에 VM을 추가합니다. 
4. Azure AD DS에 대한 조직 구성 단위를 만듭니다.
5. Azure 리소스 관리 모드에서 HDInsight VNet을 만듭니다.
6. Azure AD DS에 대한 역방향 DNS 영역을 설정합니다.
7. 두 VNet을 피어링합니다.
8. HDInsight 클러스터 만들기

> [!NOTE]
> 이 자습서에서는 사용자에게 Azure AD가 없다고 가정합니다. 갖고 있는 경우 2단계로 건너뛸 수 있습니다.
> 
> 

3단계에서 7단계까지 자동화하는 PowerShell 스크립트가 있습니다.  자세한 내용은 [Azure PowerShell을 사용하여 도메인 가입 HDInsight 클러스터 구성](hdinsight-domain-joined-configure-use-powershell.md)을 참조하세요.

## <a name="create-an-azure-classic-vnet"></a>Azure 클래식 VNet 만들기
이 섹션에서는 Azure Portal을 사용하여 클래식 VNet을 만듭니다. 다음 섹션에서는 클래식 VNet의 Azure AD에 대해 Azure AD DS를 사용하도록 설정합니다. 다음 절차 및 다른 VNet 생성 방법 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-portal.md)를 참조하세요.

**클래식 VNet을 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다. 
2. **새로 만들기** > **네트워킹** > **가상 네트워크**를 클릭합니다.
3. **배포 모델 선택**에서 **클래식**을 선택한 다음 **만들기**를 클릭합니다.
4. 다음 값을 입력하거나 선택합니다.
   
   * **이름**: contosoaadvnet
   * **주소 공간**: 10.1.0.0/16
   * **서브넷 이름**: Subnet1
   * **서브넷 주소 범위**: 10.1.0.0/24
   * **구독** - (이 VNet을 만드는 데 사용할 구독을 선택합니다.)
   * **ResourceGroup**: contosoaadrg
   * **위치**: (HDInsight 클러스터의 지역을 선택합니다.)
     
     > [!IMPORTANT]
     > Azure AD DS를 지원하는 위치를 선택해야 합니다. 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/en-us/regions/services/)을 참조하세요. 
     > 
     > 클래식 VNet과 리소스 그룹 VNet 둘 다 Azure AD DS와 동일한 지역에 있어야 합니다.
     > 
     > 
5. **만들기**를 클릭하여 VNet을 만듭니다.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Azure AD에 대한 Azure AD DS를 만들고 구성합니다.
이 섹션에서는 다음을 수행합니다.

1. Azure AD를 만듭니다.
2. Azure AD 사용자를 만듭니다. 이러한 사용자는 도메인 사용자입니다. 첫 번째 사용자는 Azure AD로 HDInsight 클러스터를 구성하는 데 사용됩니다.  이 자습서에서 다른 두 사용자는 선택 사항입니다. 두 사용자는 Apache Ranger 정책을 구성할 때 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)에 사용됩니다.
3. AAD DC Administrators 그룹을 만들고 이 그룹에 Azure AD 사용자를 추가합니다. 이 사용자를 사용하여 조직 구성 단위를 만듭니다.
4. Azure AD에 대해 Azure AD DS(Azure AD Domain Services)를 사용하도록 설정합니다.
5. Azure AD에 대해 LDAPS를 구성합니다. LDAP(Lightweight Directory Access Protocol)는 Azure AD에서 읽고 쓰는 데 사용됩니다.

기존 Azure AD를 사용하려는 경우 1, 2단계를 건너뛸 수 있습니다.

**Azure AD를 만들려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **새로 만들기** > **App Services** > **Active Directory** > **디렉터리** > **사용자 지정 만들기**를 클릭합니다. 
2. 다음 값을 입력하거나 선택합니다.
   
   * **이름**: contosoaaddirectory
   * **도메인 이름**: contoso.  이 이름은 전역적으로 고유해야 합니다.
   * **국가 또는 지역**: 국가 또는 지역을 선택합니다.
3. **완료**를 클릭합니다.

**Azure AD 사용자 만들기**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory** -> **contosoaaddirectory**를 클릭합니다. 
2. 위쪽 메뉴에서 **사용자**를 클릭합니다.
3. **사용자 추가**를 클릭합니다.
4. **사용자 이름**을 입력하고 **다음**을 클릭합니다. 
5. 사용자 프로필을 구성합니다. **역할**에서 **전역 관리자**를 클릭하고 **다음**을 클릭합니다.  조직 구성 단위를 만들려면 전역 관리자 역할이 필요합니다.
6. **만들기**를 클릭하여 임시 암호를 가져옵니다.
7. 암호의 복사본을 만들고 **완료**를 클릭합니다. 이 자습서의 뒷부분에서 이 전역 관리 사용자로 관리자 VM에 로그온하여 조직 구성 단위를 만들고 역방향 DNS를 구성할 것입니다.

동일한 절차에 따라 **사용자** 역할 hiveuser1 및 hiveuser2로 사용자 2명을 더 만듭니다. 다음 사용자는 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)에 사용됩니다.

**AAD DC 관리자 그룹을 만들고 Azure AD 사용자를 추가하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory** > **contosoaaddirectory**를 클릭합니다. 
2. 위쪽 메뉴에서 **그룹**을 클릭합니다.
3. **그룹 추가** 또는 **그룹 추가**를 클릭합니다.
4. 다음 값을 입력하거나 선택합니다.
   
   * **이름**: AAD DC 관리자.  그룹 이름을 변경하지 마세요.
   * **그룹 종류**: 보안 그룹.
5. **완료**를 클릭합니다.
6. **AAD DC 관리자**를 클릭하여 그룹을 엽니다.
7. **멤버 추가**를 클릭합니다.
8. 이전 단계에서 만든 첫 번째 사용자를 선택한 다음 **완료**를 클릭합니다.
9. 동일한 단계를 반복하여 **HiveUsers**라는 또 다른 그룹을 만들고, 두 Hive 사용자를 이 그룹에 추가합니다.

자세한 내용은 [Azure AD Domain Services(미리 보기) - 'AAD DC Administrators' 그룹 만들기](../active-directory-domain-services/active-directory-ds-getting-started.md)를 참조하세요.

**Azure AD에 대해 Azure AD DS를 사용하도록 설정하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory** > **contosoaaddirectory**를 클릭합니다. 
2. 위쪽 메뉴에서 **구성**을 클릭합니다.
3. 아래에 있는 **Domain Services**로 스크롤하여 다음 값을 설정합니다.
   
   * **이 디렉터리에 대해 도메인 서비스 사용**: 예.
   * **도메인 서비스의 DNS 도메인 이름**: Azure 디렉터리의 기본 DNS 이름을 표시합니다. 예: contoso.onmicrosoft.com.
   * **이 가상 네트워크에 도메인 서비스 연결**: 앞에서 만든 클래식 가상 네트워크(즉, **contosoaadvnet**)를 선택합니다.
4. 페이지 맨 아래에서 **저장**을 클릭합니다. **이 디렉터리에 대해 도메인 서비스 사용** 옆에 **보류 중...**이 표시됩니다.  
5. **보류 중...**이 사라질 때까지 기다리면 **IP 주소**가 채워집니다. 두 개의 IP 주소가 채워집니다. 두 주소는 Domain Services에서 프로비전한 도메인 컨트롤러의 IP 주소입니다. 해당 도메인 컨트롤러가 프로비전되어 준비가 완료되면 각 IP 주소를 볼 수 있습니다. 두 IP 주소를 적어 두세요. 나중에 필요합니다.

자세한 내용은 [Azure AD Domain Services(미리 보기) - Azure AD Domain Services 활성화](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md)를 참조하세요.

**암호를 동기화하려면**

고유의 도메인을 사용하는 경우 암호를 동기화해야 합니다. [클라우드 전용 Azure AD 디렉터리에 대해 Azure AD 도메인 서비스에 대한 암호 동기화 활성화](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)를 참조하세요.

**Azure AD에 대해 LDAPS를 구성하려면**

1. 도메인의 서명 기관에서 서명한 SSL 인증서를 가져옵니다. 자체 서명된 인증서는 사용할 수 없습니다. SSL 인증서를 가져올 수 없는 경우 hdipreview@microsoft.com에 예외에 대해 문의하십시오.
2. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory** > **contosoaaddirectory**를 클릭합니다. 
3. 위쪽 메뉴에서 **구성**을 클릭합니다.
4. 아래에 있는 **도메인 서비스**로 스크롤합니다.
5. **인증서 구성**을 클릭합니다.
6. 지침에 따라 인증서 파일 및 암호를 지정합니다. **이 디렉터리에 대해 도메인 서비스 사용** 옆에 **보류 중...**이 표시됩니다.  
7. **보류 중...**이 사라질 때까지 기다리면 **보안 LDAP 인증서**가 채워집니다.  이 작업은 10분 이상 걸릴 수 있습니다.

> [!NOTE]
> Azure AD DS에서 일부 백그라운드 작업이 실행되고 있으면 인증서를 업로드하는 동안 다음 오류가 표시될 수 있습니다. <i>이 테넌트에 대해 수행되고 있는 작업이 있습니다. 나중에 다시 시도하십시오</i>.  이 오류가 발생하면 잠시 후 다시 시도하세요. 두 번째 도메인 컨트롤러 IP는 프로비전까지 최대 3시간이 걸릴 수 있습니다.
> 
> 

자세한 내용은 [Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)을 참조하세요.

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>조직 구성 단위 및 역방향 DNS 구성
이 섹션에서는 조직 구성 단위 및 역방향 DNS를 구성할 수 있도록 Azure AD VNet에 가상 컴퓨터를 추가하고 VM에 관리 도구를 설치합니다. 역방향 DNS 조회는 Kerberos 인증에 필요합니다.

**가상 네트워크에 가상 컴퓨터를 만들려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **새로 만들기** > **계산** > **가상 컴퓨터** > **갤러리에서**를 클릭합니다.
2. 이미지를 선택한 후 **다음**을 클릭합니다.  어떤 것을 사용할지 잘 모르겠으면 기본값인 **Windows Server 2012 R2 Datacenter**를 선택합니다.
3. 다음 값을 입력하거나 선택합니다.
   
   * 가상 컴퓨터 이름: **contosoaadadmin**
   * 계층: **기본**
   * 새 사용자 이름: (사용자 이름 입력)
   * 암호: (암호 입력)
     
     사용자 이름 및 암호는 로컬 관리자입니다.
4. **다음**
5. **지역/가상 네트워크**에서, 마지막 단계에서 만든 새 가상 네트워크(contosoaadvnet)를 선택하고 **다음**을 클릭합니다.
6. 페이지 맨 아래에 있는 **완료**을 참조하세요.

**RDP로 VM에 연결하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **가상 컴퓨터** > **contosoaadadmin**을 클릭합니다.
2. 위쪽 메뉴에서 **대시보드** 를 클릭합니다.
3. 페이지 아래쪽에서 **연결**을 클릭합니다.
4. 지침에 따라 로컬 관리자 이름 및 암호를 사용합니다.

**VM을 Azure AD 도메인에 가입하려면**

1. RDP 세션에서 **시작**을 클릭한 다음 **서버 관리자**를 클릭합니다.
2. 왼쪽 메뉴에서 **로컬 서버**를 클릭합니다.
3. 작업 그룹에서 **작업 그룹**을 클릭합니다.
4. **변경**을 클릭합니다.
5. **도메인**을 클릭하고, **contoso.onmicrosoft.com**을 입력한 다음 **확인**을 클릭합니다.
6. 도메인 사용자 자격 증명을 입력한 다음 **확인**을 클릭합니다.
7. **확인**을 클릭합니다.
8. **확인**을 클릭하여 컴퓨터 다시 부팅에 동의합니다.
9. **닫기**를 클릭합니다.
10. **지금 다시 시작**을 클릭합니다.

자세한 내용은 [Windows Server 가상 컴퓨터를 관리되는 도메인에 가입](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md)을 참조하세요.

**Active Directory 관리 도구 및 DNS 도구를 설치하려면**

1. Azure AD 사용자 계정을 사용하여 RDP로 **contosoaadadmin**에 연결합니다.
2. **시작**을 클릭한 다음 **서버 관리자**를 클릭합니다.
3. 왼쪽 메뉴에서 **대시보드**를 클릭합니다.
4. **관리**를 클릭한 다음 **역할 및 기능 추가**를 클릭합니다.
5. **다음**을 클릭합니다.
6. **역할 기반 또는 기능 기반 설치**를 선택하고 **다음**을 클릭합니다.
7. 서버 풀의 현재 가상 컴퓨터를 선택하고 **다음**을 클릭합니다.
8. **다음**을 클릭하여 역할을 건너뜁니다.
9. **원격 서버 관리 도구**를 확장하고, **역할 관리 도구**를 확장하고, **AD DS 및 AD LDS 도구**와 **DNS 서버 도구**를 선택한 후 **다음**을 클릭합니다. 
10. **다음**
11. **Install**을 클릭합니다.

자세한 내용은 [가상 컴퓨터에 Active Directory 관리 도구 설치](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine)를 참조하세요.

**역방향 DNS를 구성 하려면**

1. Azure AD 사용자 계정을 사용하여 RDP로 contosoaadadmin에 연결합니다.
2. **시작**을 클릭하고, **관리 도구**를 클릭한 다음 **DNS**를 클릭합니다. 
3. **아니요**를 클릭하여 ContosoAADAdmin 추가를 건너뜁니다.
4. **다음 컴퓨터**를 선택하고, 앞에서 구성한 첫 번째 DNS 서버의 IP 주소를 입력한 다음 **확인**을 클릭합니다.  왼쪽 창에 DC/DNS가 추가된 것이 보일 것입니다.
5. DC/DNS 서버를 확장하고, **역방향 조회 영역**을 마우스 오른쪽 단추로 클릭한 다음 **새 영역**을 클릭합니다. 새 영역 마법사가 열립니다.
6. **다음**을 클릭합니다.
7. **주 영역**을 클릭하고 **다음**을 클릭합니다.
8. **이 도메인의 도메인 컨트롤러에서 실행되는 모든 DNS 서버에**를 선택하고 **다음**을 클릭합니다.
9. **IPv4 역방향 조회 영역**을 선택하고 **다음**을 클릭합니다.
10. **네트워크 ID**에서 HDInsight VNET 네트워크 범위의 접두사를 입력하고 **다음**을 클릭합니다. 다음 섹션에서는 HDInsight VNet을 만들겠습니다.
11. **다음**을 클릭합니다.
12. **Next**를 클릭합니다.
13. **마침**을 클릭합니다.

다음으로 만들 조직 구성 단위는 HDInsight 클러스터를 만들 때 사용됩니다. Hadoop 시스템 사용자 및 컴퓨터 계정은 이 OU에 배치됩니다.

**Azure AD Domain Services 관리되는 도메인에 OU(조직 구성 단위) 만들기**

1. **AAD DC Administrators** 그룹에 있는 도메인 계정을 사용하여 RDP로 **contosoaadadmin**에 연결합니다.
2. **시작**을 클릭하고, **관리 도구**를 클릭한 다음 **Active Directory 관리 센터**를 클릭합니다.
3. 왼쪽 창에서 도메인 이름을 클릭합니다. 예: contoso.
4. **작업** 창의 도메인 이름 아래에서 **새로 만들기**를 클릭한 다음 **조직 구성 단위**를 클릭합니다.
5. 이름을 입력(예: **HDInsightOU**)한 다음 **확인**을 클릭합니다. 

자세한 내용은 [Azure AD Domain Services 관리되는 도메인에 OU(조직 구성 단위) 만들기](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 참조하세요.

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>HDInsight 클러스터에 대한 Resource Manager VNet 만들기
이 섹션에서는 HDInsight 클러스터에 사용할 Azure Resource Manager VNet을 만들겠습니다. Azure VNET을 만드는 다른 방법에 대한 자세한 내용은 [가상 네트워크 만들기](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)를 참조하세요.

VNet을 만든 후에는 Azure AD VNet과 동일한 DNS 서버를 사용하도록 Resource Manager VNet을 구성하겠습니다. 이 자습서의 단계에 따라 클래식 VNet 및 Azure AD를 만들었다면 DNS 서버는 10.1.0.4 및 10.1.0.5입니다.

**Resource Manager VNet을 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **네트워킹**, **가상 네트워크**를 차례로 클릭합니다. 
3. **배포 모델 선택**에서 **Resource Manager**를 선택하고 **만들기**를 클릭합니다.
4. 다음 값을 입력하거나 선택합니다.
   
   * **이름**: contosohdivnet
   * **주소 공간**: 10.2.0.0/16. 주소 범위는 클래식 VNet의 IP 주소 범위와 겹칠 수 없습니다.
   * **서브넷 이름**: Subnet1
   * **서브넷 주소 범위**: 10.2.0.0/24
   * **구독**: (Azure 구독을 선택합니다.)
   * **리소스 그룹**: contosohdirg
   * **위치**: (Azure AD VNet과 동일한 위치, 즉 contosoaadvnet을 선택합니다.)
5. **만들기**를 클릭합니다.

**Resource Manager VNet에 대해 DNS를 구성하려면**

1. [Azure Portal](https://portal.azure.com)에서 **더 많은 서비스** -> **가상 네트워크**를 클릭합니다. **가상 네트워크(클래식)**을 클릭하지 마세요.
2. **contosohdivnet**을 클릭합니다.
3. 새 블레이드의 왼쪽에서 **DNS 서버**를 클릭합니다.
4. **사용자 지정**을 클릭하고 다음 값을 입력합니다.
   
   * 10.1.0.4
   * 10.1.0.5
     
     이러한 DNS 서버 IP 주소는 Azure AD VNet(클래식 VNet)의 DNS 서버와 일치해야 합니다.
5. **Save**를 클릭합니다.

## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Azure AD VNet과 HDInsight VNet 피어링
**두 VNet을 피어링하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **더 많은 서비스**를 클릭합니다.
3. **가상 네트워크**를 클릭합니다. **가상 네트워크(클래식)**을 클릭하지 마세요.
4. **contosohdivnet**을 클릭합니다.  이것은 HDInsight VNet입니다.
5. 블레이드의 왼쪽 메뉴에서 **피어링**을 클릭합니다.
6. 위쪽 메뉴에서 **추가**를 클릭합니다. 그러면 **피어링 추가** 블레이드가 열립니다.
7. **피어링 추가** 블레이드에서 다음 값을 설정하거나 선택합니다.
   
   * **이름**: ContosoAADHDIVNetPeering
   * **가상 네트워크 배포 모델**: 클래식
   * **구독**: 클래식(Azure AD) vnet에 사용되는 구독 이름을 선택합니다.
   * **가상 네트워크**: contosoaadvnet.
   * **가상 네트워크 액세스 허용**: (확인)
   * **전달된 트래픽 허용**: (확인). 다른 두 확인란은 선택하지 않고 그냥 둡니다.
8. **확인**을 클릭합니다.

## <a name="create-hdinsight-cluster"></a>HDInsight 클러스터 만들기
이 섹션에서는 Azure Portal 또는 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-template-deploy.md)을 사용하여 HDInsight에서 Linux 기반 Hadoop 클러스터를 만듭니다. 기타 클러스터 생성 방법 및 설정에 대한 이해는 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. Resource Manager 템플릿을 사용하여 HDInsight에서 Hadoop 클러스터를 만드는 방법에 대한 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Hadoop 클러스터 만들기](hdinsight-hadoop-create-windows-clusters-arm-templates.md)를 참조하세요.

**Azure Portal을 사용하여 도메인에 가입된 HDInsight 클러스터를 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **인텔리전스 + 분석**, **HDInsight**를 차례로 클릭합니다.
3. **새 HDInsight 클러스터** 블레이드에서 다음 값을 입력하거나 선택합니다.
   
   * **클러스터 이름**: 도메인에 가입된 HDInsight 클러스터의 새 클러스터 이름을 입력합니다.
   * **구독**: 이 클러스터를 만드는 데 사용할 Azure 구독을 선택합니다.
   * **클러스터 구성**:
     
     * **클러스터 유형**: Hadoop. 도메인에 가입된 HDInsight는 현재 Hadoop 클러스터에서만 지원됩니다.
     * **운영 체제**: Linux.  도메인에 가입된 HDInsight는 Linux 기반 HDInsight 클러스터에서만 지원됩니다.
     * **버전**: Hadoop 2.7.3(HDI 3.5). 도메인에 가입된 HDInsight는 HDInsight 클러스터 버전 3.5에서만 지원됩니다.
     * **클러스터 유형**: PREMIUM
       
       **선택**을 클릭하여 변경 내용을 저장합니다.
   * **자격 증명**: 클러스터 사용자와 SSH 사용자에 대한 자격 증명을 구성합니다.
   * **데이터 원본**: HDInsight 클러스터에 대한 기본 저장소 계정으로 사용할 새 저장소 계정을 만들거나 기존 저장소 계정을 사용합니다. 위치는 두 VNet과 동일해야 합니다.  이 위치는 HDInsight 클러스터의 위치이기도 합니다.
   * **가격 책정**: 클러스터의 작업자 노드 수를 선택합니다.
   * **고급 구성**: 
     
     * **도메인 가입 및 Vnet/Subnet**: 
       
       * **도메인 설정**: 
         
         * **도메인 이름**: contoso.onmicrosoft.com
         * **도메인 사용자 이름**: 도메인 사용자 이름을 입력합니다. 이 도메인은 컴퓨터를 도메인에 가입하고 이전에 구성한 조직 구성 단위에 배치하고, 이전에 구성한 조직 구성 단위 내에서 서비스 사용자를 만들고, 역방향 DNS 항목을 만들 수 있는 권한을 갖고 있어야 합니다. 이 도메인 사용자는 이 도메인에 가입된 HDInsight 클러스터의 관리자가 됩니다.
         * **도메인 암호**: 도메인 사용자 암호를 입력합니다.
         * **조직 구성 단위**: 이전에 구성한 OU의 고유한 이름을 입력합니다. 예: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **LDAPS URL**: ldaps://contoso.onmicrosoft.com:636
         * **사용자 액세스 그룹**: 클러스터에 동기화할 사용자의 보안 그룹을 지정합니다. 예: HiveUsers.
           
           **선택**을 클릭하여 변경 내용을 저장합니다.
           
           ![도메인에 가입된 HDInsight 도메인 설정 구성](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **가상 네트워크**: contosohdivnet
       * **서브넷**: Subnet1
         
         **선택**을 클릭하여 변경 내용을 저장합니다.        
         **선택**을 클릭하여 변경 내용을 저장합니다.
   * **리소스 그룹**: HDInsight VNet(contosohdirg)에 사용되는 리소스 그룹을 선택합니다.
4. **만들기**를 클릭합니다.  

도메인에 가입된 HDInsight 클러스터를 만드는 또 다른 방법은 Azure 리소스 관리 템플릿을 사용하는 것입니다. 아래 절차에서는 다음 작업을 수행하는 방법을 보여줍니다.

**리소스 관리 템플릿을 사용하여 도메인에 가입된 HDInsight 클러스터를 만들려면**

1. Azure Portal에서 Resource Manager 템플릿을 열려면 다음 이미지를 클릭합니다. Resource Manager 템플릿은 공용 blob 컨테이너에 있습니다. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **매개 변수** 블레이드에서 다음 값을 입력합니다.
   
   * **구독**: (Azure 구독을 선택합니다.)
   * **리소스 그룹**: **기존 항목 사용**을 클릭하고 지금까지 사용한 것과 동일한 리소스 그룹을 지정합니다.  예: contosohdirg. 
   * **위치**: 리소스 그룹 위치를 지정합니다.
   * **클러스터 이름**: 만들려는 Hadoop 클러스터의 이름을 입력합니다. 예: contosohdicluster.
   * **클러스터 유형**: 클러스터 유형을 선택합니다.  기본값은 **hadoop**입니다.
   * **위치**: 클러스터의 위치를 선택합니다.  기본 저장소 계정은 동일한 위치를 사용합니다.
   * **클러스터 작업자 노드 수**: 작업자 노드 수를 선택합니다.
   * **클러스터 로그인 이름 및 암호**: 기본 로그인 이름은 **admin**입니다.
   * **SSH 사용자 이름 및 암호**: 기본 사용자 이름은 **sshuser**입니다.  이름은 변경할 수 있습니다. 
   * **가상 네트워크 Id**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **가상 네트워크 서브넷**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **도메인 이름**: contoso.onmicrosoft.com
   * **조직 구성 단위 DN**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **클러스터 사용자 그룹 DN**: "\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPUrl**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (도메인 관리자 이름 입력)
   * **DomainAdminPassword**: (도메인 관리자 암호 입력)
   * **위에 명시된 사용 약관에 동의함**: (확인)
   * **대시보드에 고정**: (확인)
3. **구매**를 클릭합니다. **템플릿 배포 배포 중**이라는 제목의 새 타일이 표시됩니다. 클러스터를 만들려면 20분 정도가 걸립니다. 클러스터가 만들어졌으면 포털에서 클러스터 블레이드를 클릭하면 열립니다.

이 자습서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure 저장소에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 클러스터 삭제에 대한 내용은 [Azure Portal을 사용하여 HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md#delete-clusters)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Azure PowerShell을 사용하여 도메인 가입 HDInsight 클러스터를 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 도메인 가입 HDInsight 클러스터 구성](hdinsight-domain-joined-configure-use-powershell.md)을 참조하세요.
* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 도메인에 가입된 HDInsight 클러스터에 연결하려면 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.


