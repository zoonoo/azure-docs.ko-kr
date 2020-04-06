---
title: Azure NetApp 파일에 대한 SMB 볼륨 만들기 | 마이크로 소프트 문서
description: Azure NetApp 파일에 대한 SMB 볼륨을 만드는 방법에 대해 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: c4e7566eeb28bc5709acd60ced9fcdffb7e8a725
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668013"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 SMB 볼륨 만들기

Azure NetApp 파일은 NFS 및 SMBv3 볼륨을 지원합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 이 문서에서는 SMBv3 볼륨을 만드는 방법을 보여 주며 있습니다. NFS 볼륨을 만들려면 Azure [NetApp 파일에 대한 NFS 볼륨 만들기를](azure-netapp-files-create-volumes.md)참조하십시오. 

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.   
[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files에 서브넷을 위임해야 합니다.  
[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 연결에 대한 요구 사항

 SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Active Directory 연결에 대한 요구 사항은 다음과 같습니다. 

* 사용하는 관리자 계정에는 지정할 조직 단위(OU) 경로에서 컴퓨터 계정을 만들 수 있는 기능이 있어야 합니다.  

* 적절한 포트는 해당 AD(AD) 서버에서 열려 있어야 합니다.  
    필요한 포트는 다음과 같습니다. 

    |     서비스           |     포트     |     프로토콜     |
    |-----------------------|--------------|------------------|
    |    광고 웹 서비스    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    해당 없음       |    에코 응답    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS이름       |    138       |    UDP           |
    |    샘/LSA            |    445       |    TCP           |
    |    샘/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* 대상 Active Directory 도메인 서비스에 대한 사이트 토폴로지는 모범 사례, 특히 Azure NetApp 파일이 배포된 Azure VNet을 준수해야 합니다.  

    Azure NetApp 파일이 배포되는 가상 네트워크의 주소 공간은 새 또는 기존 Active Directory 사이트(Azure NetApp Files에서 연결할 수 있는 도메인 컨트롤러가 있는 경우)에 추가되어야 합니다. 

* 지정된 DNS 서버는 Azure NetApp 파일의 [위임된 서브넷에서](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) 연결할 수 있어야 합니다.  

    지원되는 네트워크 토폴로지에 [대한 Azure NetApp 파일 네트워크 계획에](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 대한 지침을 참조하십시오.

    NSG(네트워크 보안 그룹) 및 방화벽에는 Active Directory 및 DNS 트래픽 요청을 허용하도록 적절하게 구성된 규칙이 있어야 합니다. 

* Azure NetApp 파일 위임된 서브넷은 모든 로컬 및 원격 도메인 컨트롤러를 포함하여 도메인의 모든 Active Directory 도메인 서비스(ADDS) 도메인 컨트롤러에 연결할 수 있어야 합니다. 그렇지 않으면 서비스 중단이 발생할 수 있습니다.  

    Azure NetApp 파일 위임된 서브넷에서 연결할 수 없는 도메인 컨트롤러가 있는 경우 Active Directory 연결을 만드는 동안 Active Directory 사이트를 지정할 수 있습니다.  Azure NetApp 파일은 Azure NetApp 파일이 위임된 서브넷 주소 공간이 있는 사이트의 도메인 컨트롤러와만 통신해야 합니다.

    AD 사이트 및 서비스에 대한 [사이트 토폴로지 설계를](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) 참조하십시오. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

추가 광고 정보에 대한 Azure NetApp 파일 [SMB 자주 묻는 질문(FAQ)을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) 참조하십시오. 

## <a name="decide-which-domain-services-to-use"></a>사용할 도메인 서비스 결정 

Azure NetApp 파일은 AD 연결에 대한 [Active Directory 도메인](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) 서비스(ADDS) 및 Azure Active Directory 도메인 서비스(AADDS)를 모두 지원합니다.  AD 연결을 만들기 전에 ADDS 또는 AADDS를 사용할지 여부를 결정해야 합니다.  

자세한 내용은 [자체 관리되는 Active Directory 도메인 서비스, Azure Active Directory 및 관리되는 Azure Active Directory 도메인 서비스 비교를](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)참조하십시오. 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Azure NetApp 파일에 대해 기본 [코드 디렉터리 사이트 및 서비스](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) 범위를 사용할 수 있습니다. 이 옵션을 사용하면 [Azure NetApp 파일에서 액세스할 수](azure-netapp-files-network-topologies.md)있는 활성 디렉터리 도메인 서비스(ADDS) 도메인 컨트롤러를 읽고 쓸 수 있습니다. 또한 서비스가 지정된 Active Directory 사이트 및 서비스 사이트에 없는 도메인 컨트롤러와 통신하지 못하도록 합니다. 

ADDS를 사용할 때 사이트 이름을 찾으려면 Active Directory 도메인 서비스를 담당하는 조직의 관리 그룹에 문의할 수 있습니다. 아래 예제에서는 사이트 이름이 표시되는 Active Directory 사이트 및 서비스 플러그인을 보여 주며 다음과 같습니다. 

![Active Directory 사이트 및 서비스](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Azure NetApp 파일에 대한 AD 연결을 구성할 때 **AD 사이트** 이름 필드의 범위에 사이트 이름을 지정합니다.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory 도메인 서비스(AADDS) 구성 및 지침은 [Azure AD 도메인 서비스 설명서를](https://docs.microsoft.com/azure/active-directory-domain-services/)참조하십시오.

Azure NetApp 파일에 는 추가 AADDS 고려 사항이 적용됩니다. 

* AADDS가 배포되는 VNet 또는 서브넷이 Azure NetApp 파일 배포와 동일한 Azure 지역에 있는지 확인합니다.
* Azure NetApp 파일이 배포된 리전에서 다른 VNet을 사용하는 경우 두 VNet 간에 피어링을 만들어야 합니다.
* Azure NetApp `user` 파일은 지원 및 `resource forest` 형식을 지원합니다.
* 동기화 유형의 경우 를 `All` 선택하거나 `Scoped`.   
    을 선택하면 `Scoped`SMB 공유에 액세스하기 위해 올바른 Azure AD 그룹이 선택되어 있는지 확인합니다.  확실하지 않은 경우 `All` 동기화 유형을 사용할 수 있습니다.
* 엔터프라이즈 또는 프리미엄 SKU를 사용해야 합니다. 표준 SKU는 지원되지 않습니다.

Active Directory 연결을 만들 때 AADDS에 대한 다음 세부 사항을 기록합니다.

* AADDS 메뉴에서 **기본 DNS,** **보조 DNS**및 **AD DNS 도메인 이름에** 대한 정보를 찾을 수 있습니다.  
DNS 서버의 경우 Active Directory 연결을 구성하는 데 두 개의 IP 주소가 사용됩니다. 
* **조직 단위 경로는** . `OU=AADDC Computers`  
이 설정은 **NetApp 계정의** **Active Directory 연결에서** 구성됩니다.

  ![조직 단위 경로](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **사용자 이름** 자격 증명은 Azure AD 그룹 Azure **AD DC 관리자의**구성원인 모든 사용자일 수 있습니다.


## <a name="create-an-active-directory-connection"></a>Active 디렉터리 연결 만들기

1. NetApp 계정에서 활성 **디렉터리 연결을**클릭한 다음 **가입을**클릭합니다.  

    ![활성 디렉터리 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 활성 디렉터리 가입 창에서 사용하려는 도메인 서비스를 기반으로 다음 정보를 제공합니다.  

    사용하는 도메인 서비스와 관련된 정보는 을 [사용할 도메인 서비스 결정 을](#decide-which-domain-services-to-use)참조하십시오. 

    * **기본 DNS**  
        Active Directory 도메인 조인 및 SMB 인증 작업에 필요한 DNS입니다. 
    * **보조 DNS**   
        중복 이름 서비스를 보장하기 위한 보조 DNS 서버입니다. 
    * **AD DNS 도메인 이름**  
        이 이름은 가입하려는 Active Directory 도메인 서비스의 도메인 이름입니다.
    * **광고 사이트 이름**  
        도메인 컨트롤러 검색이 제한되는 사이트 이름입니다.
    * **SMB 서버(컴퓨터 계정) 접두사**  
        Azure NetApp 파일이 새 계정을 만드는 데 사용할 Active Directory의 컴퓨터 계정에 대한 명명 접두사입니다.

        예를 들어 조직에서 파일 서버에 사용하는 명명 표준이 NAS-01, NAS-02..., NAS-045인 경우 접두사에 대해 "NAS"를 입력합니다. 

        서비스는 필요에 따라 Active Directory에서 추가 컴퓨터 계정을 만듭니다.

    * **조직 단위 경로**  
        SMB 서버 컴퓨터 계정이 생성되는 조직 단위(OU)의 LDAP 경로입니다. 즉, OU=두 번째 수준, OU=첫 번째 수준입니다. 

        Azure Active Directory 도메인 서비스와 함께 Azure NetApp 파일을 사용하는 `OU=AADDC Computers` 경우 조직 단위 경로는 NetApp 계정에 대해 Active Directory를 구성할 때입니다.
        
    * **사용자 이름** 및 **암호를** 포함한 자격 증명

    ![활성 디렉토리 가입](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **조인**을 클릭합니다.  

    만든 Active Directory 연결이 나타납니다.

    ![활성 디렉터리 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Active Directory 연결을 저장한 후 사용자 이름 및 암호 필드를 편집할 수 있습니다. 연결을 저장한 후에는 다른 값을 편집할 수 없습니다. 다른 값을 변경해야 하는 경우 먼저 배포된 SMB 볼륨을 삭제한 다음 Active Directory 연결을 삭제하고 다시 만들어야 합니다.

## <a name="add-an-smb-volume"></a>SMB 볼륨 추가

1. 용량 풀 블레이드에서 **볼륨** 블레이드를 클릭합니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    볼륨 만들기 창이 나타납니다.

3. 볼륨 만들기 창에서 **만들기를** 클릭하고 다음 필드에 대한 정보를 제공합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유해야 합니다. 3자 이상이어야 합니다. 모든 사용 문자는 사용할 수 있습니다.   

        볼륨 이름으로 사용할 `default` 수 없습니다.

    * **용량 풀**  
        볼륨을 만들 용량 풀을 지정합니다.

    * **할당량**  
        볼륨에 할당되는 논리 스토리지의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스하려는 Azure 가상 네트워크(VNet)를 지정합니다.  

        지정한 VNet에는 Azure NetApp 파일에 위임된 서브넷이 있어야 합니다. Azure NetApp 파일 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 지역에 있는 VNet에서만 액세스할 수 있습니다. 익스프레스 루트를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우 볼륨 만들기 페이지에서 **새 만들기를** 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp 파일에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **프로토콜을** 클릭하고 다음 정보를 완료합니다.  
    * 볼륨의 프로토콜 유형으로 **SMB를** 선택합니다. 
    * 드롭다운 목록에서 **Active Directory** 연결을 선택합니다.
    * 공유 볼륨의 이름을 **공유 이름에서**지정합니다.

    ![SMB 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **검토 + 만들기를** 클릭하여 볼륨 세부 정보를 검토합니다.  그런 다음 **만들기를** 클릭하여 SMB 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 나타납니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [중소기업 자주 묻는 질문](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 자세히 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure CLI를 사용하여 새 Active Directory 포리스트 설치](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
