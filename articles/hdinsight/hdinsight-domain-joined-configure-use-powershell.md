---
title: "Azure PowerShell을 사용하여 도메인 가입 HDInsight 클러스터 구성 | Microsoft 문서"
description: "Azure PowerShell을 사용하여 도메인 가입 HDInsight 클러스터를 설정 및 구성하는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
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
ms.openlocfilehash: 916722d64f191bc482b50ad4338f6a1da1045b77
ms.lasthandoff: 03/25/2017


---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Azure PowerShell을 사용하여 도메인 가입 HDInsight 클러스터 구성(미리 보기)
Azure PowerShell을 사용하여 Azure AD(Azure Active Directory) 및 [Apache Ranger](http://hortonworks.com/apache/ranger/)와 함께 Azure HDInsight 클러스터를 설정하는 방법에 대해 알아봅니다. Azure PowerShell 스크립트는 보다 빠르게 구성하고 구성 오류를 줄이기 위해 제공됩니다. 도메인에 가입된 HDInsight는 Linux 기반 클러스터에만 구성할 수 있습니다. 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 소개](hdinsight-domain-joined-introduction.md)를 참조하세요.

일반적인 도메인 가입 HDInsight 클러스터 구성 단계는 다음과 같습니다.

1. Azure AD에 대한 Azure 클래식 VNet을 만듭니다.  
2. Azure AD 및 Azure AD DS를 만들고 구성합니다.
3. 조직 구성 단위를 만들기 위해 클래식 VNet에 VM을 추가합니다. 
4. Azure AD DS에 대한 조직 구성 단위를 만듭니다.
5. Azure 리소스 관리 모드에서 HDInsight VNet을 만듭니다.
6. Azure AD DS에 대한 역방향 DNS 영역을 설정합니다.
7. 두 VNet을 피어링합니다.
8. HDInsight 클러스터 만들기

제공되는 PowerShell 스크립트는 3-7단계를 수행합니다. 1단계와 2단계는 수동으로 수행해야 합니다.  Azure PowerShell을 사용하지 않으려면 [도메인 가입 HDInsight 클러스터 구성](hdinsight-domain-joined-configure.md)을 참조하세요. 

최종 토폴로지의 예는 다음과 같습니다.

![도메인에 가입된 HDInsight 토폴로지](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

현재 Azure AD는 클래식 가상 네트워크(VNet)만 지원하고 Linux 기반 HDInsight 클러스터는 Azure Resource Manager 기반 VNet만 지원하므로 HDInsight Azure AD를 통합하려면 두 VNet이 있어야 하고 또 두 VNet을 피어링해야 합니다. 두 배포 모델의 비교 정보는 [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요. 두 VNet이 Azure AD DS와 동일한 지역에 있어야 합니다.

> [!NOTE]
> 이 자습서에서는 사용자에게 Azure AD가 없다고 가정합니다. 갖고 있는 경우 2단계로 건너뛸 수 있습니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 진행하려면 다음 항목이 필요합니다.

* [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) 및 [가격 책정](https://azure.microsoft.com/pricing/details/active-directory-ds/) 구조를 숙지합니다.
* 구독이 이 공개 미리 보기에 대한 허용 목록에 추가되었는지 확인합니다. 구독 ID로 hdipreview@microsoft.com에 전자 메일을 전송하여 추가할 수 있습니다.
* 도메인의 서명 기관에서 서명한 SSL 인증서가 필요합니다. 이 인증서는 보안 LDAP를 구성하는 데 필요합니다. 자체 서명된 인증서는 사용할 수 없습니다.
* Azure PowerShell.  [Azure PowerShell 설치 및 구성](/powershell/azureps-cmdlets-docs)을 참조하세요.

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Azure AD에 대한 Azure 클래식 VNet을 만듭니다.
지침에 대해서는 [여기](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet)를 참조하세요.

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Azure AD 및 Azure AD DS를 만들고 구성합니다.
지침에 대해서는 [여기](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad)를 참조하세요.

## <a name="run-the-powershell-script"></a>PowerShell 스크립트 실행
PowerShell 스크립트는 [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight)에서 다운로드할 수 있습니다. Zip 파일의 압축을 풀고 파일을 로컬로 저장합니다.

**PowerShell 스크립트를 편집하려면**

1. Windows PowerShell ISE 또는 다른 텍스트 편집기를 사용하여 run.ps1을 엽니다.
2. 다음 변수의 값을 채웁니다.
   
   * **$SubscriptionName** - HDInsight 클러스터를 만들 Azure 구독의 이름입니다. 클래식 가상 네트워크는 이미 이 구독에 만들어졌으며, 구독 중인 HDInsight 클러스터를 위한 Azure Resource Manager 가상 네트워크를 만들 것입니다.
   * **$ClassicVNetName** - Azure AD DS가 포함된 클래식 가상 네트워크입니다. 이 가상 네트워크는 위에서 제공된 동일한 구독에 있어야 합니다. 이 가상 네트워크는 클래식 포털을 사용하지 않고 Azure Portal을 사용하여 만들어야 합니다. [도메인 가입 HDInsight 클러스터 구성(미리 보기)](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet)의 지침을 따르는 경우 기본 이름은 contosoaadvnet입니다.
   * **$ClassicResourceGroupName** - 위에서 언급한 클래식 가상 네트워크의 Resource Manager 그룹 이름입니다. 예를 들어 contosoaadrg과 같습니다. 
   * **$ArmResourceGroupName** - HDInsight 클러스터를 만들려는 리소스 그룹 이름입니다. $ArmResourceGroupName과 동일한 리소스 그룹을 사용할 수 있습니다.  리소스 그룹이 없으면 스크립트에서 리소스 그룹을 만듭니다.
   * **$ArmVNetName** - HDInsight 클러스터를 만들려는 Resource Manager 가상 네트워크 이름입니다. 이 가상 네트워크는 $ArmResourceGroupName에 배치됩니다.  VNet이 없으면 PowerShell 스크립트에서 해당 VNet을 만듭니다. VNet이 있으면 위에서 제공하는 리소스 그룹의 일부여야 합니다.
   * **$AddressVnetAddressSpace** - Resource Manager 가상 네트워크의 네트워크 주소 공간입니다. 이 주소 공간을 사용할 수 있는지 확인합니다. 이 주소 공간은 클래식 가상 네트워크 주소 공간과 겹칠 수 없습니다. 예를 들어 "10.1.0.0/16"과 같습니다.
   * **$ArmVnetSubnetName** - HDInsight 클러스터 VM을 배치할 Resource Manager 가상 네트워크 서브넷 이름입니다. 서브넷이 없으면 PowerShell 스크립트에서 해당 서브넷을 만듭니다. 서브넷이 있으면 위에서 제공하는 가상 네트워크의 일부여야 합니다.
   * **$AddressSubnetAddressSpace** - Resource Manager 가상 네트워크 서브넷의 네트워크 주소 범위입니다. HDInsight 클러스터 VM IP 주소는 이 서브넷 주소 범위에 있습니다. 예를 들어 "10.1.0.0/24"와 같습니다.
   * **$ActiveDirectoryDomainName** - HDInsight 클러스터 VM에 가입시키려는 Azure AD 도메인 이름입니다. 예를 들어 "contoso.onmicrosoft.com"과 같습니다.
   * **$ClusterUsersGroups** - HDInsight 클러스터에 동기화하려는 AD의 보안 그룹의 일반 이름입니다. 이 보안 그룹 내의 사용자는 AD 도메인 자격 증명을 사용하여 클러스터 대시보드에 로그온할 수 있습니다. 이러한 보안 그룹은 AD에 있어야 합니다. 예를 들어 "hiveusers" 또는 "clusteroperatorusers"과 같습니다.
   * **$OrganizationalUnitName** - HDInsight 클러스터 VM을 배치할 도메인의 OU(조직 구성 단위) 및 클러스터에서 사용하는 서비스 주체입니다. OU가 없으면 PowerShell 스크립트에서 해당 OU를 만듭니다. 예를 들어 "HDInsightOU"와 같습니다.
3. 변경 내용을 저장합니다.

**스크립트를 실행 하려면**

1. 관리자 권한으로 **Windows PowerShell**을 실행합니다.
2. run.ps1의 폴더를 찾습니다. 
3. 파일 이름을 입력하여 스크립트를 실행하고 **Enter** 키를 누릅니다.  다음과 같은 3개의 로그인 대화 상자가 나타납니다.
   
   1. **Azure 클래식 포털에 로그인** - Azure 클래식 포털에 로그인하는 데 사용하는 자격 증명을 입력합니다. 이러한 자격 증명을 사용하여 Azure AD 및 Azure AD DS를 만들어야 합니다.
   2. **Azure Resource Manager 포털에 로그인** - Azure Resource Manager 포털에 로그인하는 데 사용하는 자격 증명을 입력합니다.
   3. **도메인 사용자 이름** - HDInsight 클러스터에서 관리자로 지정할 도메인 사용자 이름의 자격 증명을 입력합니다. 처음부터 Azure AD를 만든 경우 이 설명서를 사용하여 해당 사용자를 만들어야 합니다. 
      
      > [!IMPORTANT]
      > 다음 형식으로 사용자 이름을 입력합니다. 
      > 
      > Domainname\username(예: contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      이 사용자에게는 3가지 권한이 있어야 합니다. 즉 제공된 Active Directory 도메인에 컴퓨터를 가입시키고, 제공된 조직 구성 단위 내에서 서비스 주체와 컴퓨터 개체를 만들며, 역방향 DNS 프록시 규칙을 추가합니다.

역방향 DNS 영역을 만드는 동안 스크립트에서는 네트워크 ID를 입력하라는 메시지를 표시합니다. 이 네트워크 ID는 Resource Manager 가상 네트워크의 주소 접두사여야 합니다. 예를 들어 Resource Manager 가상 네트워크 서브넷 주소 공간이 10.2.0.0/24인 경우 도구에서 네트워크 ID를 요청하는 메시지를 표시하면 10.2.0.0/24를 입력합니다. 

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
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
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

* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 도메인에 가입된 HDInsight 클러스터에 연결하려면 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.


