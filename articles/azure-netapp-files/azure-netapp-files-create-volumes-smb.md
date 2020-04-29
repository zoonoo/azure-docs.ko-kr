---
title: Azure NetApp Files에 대 한 SMB 볼륨 만들기 | Microsoft Docs
description: Azure NetApp Files에 대 한 SMB 볼륨을 만드는 방법을 설명 합니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668013"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 SMB 볼륨 만들기

Azure NetApp Files는 NFS 및 SMBv3 볼륨을 지원 합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 이 문서에서는 SMBv3 볼륨을 만드는 방법을 보여 줍니다. NFS 볼륨을 만들려면 [Azure NetApp Files에 대 한 nfs 볼륨 만들기](azure-netapp-files-create-volumes.md)를 참조 하세요. 

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.   
[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files에 서브넷을 위임해야 합니다.  
[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 연결에 대 한 요구 사항

 SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Active Directory 연결에 대 한 요구 사항은 다음과 같습니다. 

* 사용 하는 관리자 계정에는 지정할 OU (조직 구성 단위) 경로에 컴퓨터 계정을 만들 수 있는 기능이 있어야 합니다.  

* 해당 하는 Windows Active Directory (AD) 서버에서 적절 한 포트가 열려 있어야 합니다.  
    필요한 포트는 다음과 같습니다. 

    |     서비스           |     포트     |     프로토콜     |
    |-----------------------|--------------|------------------|
    |    AD 웹 서비스    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    해당 없음       |    Echo Reply    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 이름       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* 대상 Active Directory Domain Services에 대 한 사이트 토폴로지는 Azure NetApp Files 배포 되는 Azure VNet의 모범 사례를 준수 해야 합니다.  

    Azure NetApp Files 배포 되는 가상 네트워크의 주소 공간은 새 사이트 또는 기존 Active Directory 사이트에 추가 해야 합니다 (Azure NetApp Files에서 연결할 수 있는 도메인 컨트롤러는). 

* Azure NetApp Files의 [위임 된 서브넷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) 에서 지정 된 DNS 서버에 연결할 수 있어야 합니다.  

    지원 되는 네트워크 토폴로지의 [Azure NetApp Files 네트워크 계획에 대 한 지침을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 참조 하세요.

    NSGs (네트워크 보안 그룹) 및 방화벽에는 Active Directory 및 DNS 트래픽 요청을 허용 하는 적절 하 게 구성 된 규칙이 있어야 합니다. 

* Azure NetApp Files 위임 된 서브넷은 모든 로컬 및 원격 도메인 컨트롤러를 포함 하 여 도메인의 모든 Active Directory Domain Services (추가) 도메인 컨트롤러에 연결할 수 있어야 합니다. 그렇지 않으면 서비스 중단이 발생할 수 있습니다.  

    Azure NetApp Files 위임 된 서브넷에서 연결할 수 없는 도메인 컨트롤러가 있는 경우 Active Directory 연결을 만드는 동안 Active Directory 사이트를 지정할 수 있습니다.  Azure NetApp Files은 Azure NetApp Files 위임 된 서브넷 주소 공간이 인 사이트의 도메인 컨트롤러와만 통신 해야 합니다.

    AD 사이트 및 서비스에 대 한 [사이트 토폴로지 디자인을](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) 참조 하세요. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

추가 AD 정보에 대 한 Azure NetApp Files [SMB faq](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) 를 참조 하세요. 

## <a name="decide-which-domain-services-to-use"></a>사용할 도메인 서비스 결정 

Azure NetApp Files는 AD 연결에 대 한 [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (추가) 및 Azure Active Directory Domain Services (aadds)를 모두 지원 합니다.  AD 연결을 만들기 전에 추가를 사용할지 아니면 추가를 사용할지를 결정 해야 합니다.  

자세한 내용은 [자체 관리 되는 Active Directory Domain Services, Azure Active Directory 및 관리 되는 Azure Active Directory Domain Services 비교](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)를 참조 하세요. 

### <a name="active-directory-domain-services"></a>Active Directory 도메인 서비스

Azure NetApp Files에 대해 기본 설정 [Active Directory 사이트 및 서비스](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) 범위를 사용할 수 있습니다. 이 옵션은 [Azure NetApp Files에서 액세스할](azure-netapp-files-network-topologies.md)수 있는 도메인 컨트롤러 ACTIVE DIRECTORY DOMAIN SERVICES (추가)에 대 한 읽기 및 쓰기를 가능 하 게 합니다. 또한 서비스가 지정 된 Active Directory 사이트 및 서비스 사이트에 없는 도메인 컨트롤러와 통신 하는 것을 방지 합니다. 

추가를 사용할 때 사이트 이름을 찾으려면 조직에서 Active Directory Domain Services를 담당 하는 관리 그룹에 연결할 수 있습니다. 아래 예제에서는 사이트 이름이 표시 되는 Active Directory 사이트 및 서비스 플러그 인을 보여 줍니다. 

![Active Directory 사이트 및 서비스](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Azure NetApp Files에 대 한 AD 연결을 구성 하는 경우 **Ad 사이트 이름** 필드의 범위에 사이트 이름을 지정 합니다.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services (AADDS) 구성 및 지침은 [Azure AD Domain Services 설명서](https://docs.microsoft.com/azure/active-directory-domain-services/)를 참조 하세요.

추가 추가 고려 사항은 Azure NetApp Files에 적용 됩니다. 

* AADDS가 배포 된 VNet 또는 서브넷이 Azure NetApp Files 배포와 동일한 Azure 지역에 있는지 확인 합니다.
* Azure NetApp Files 배포 된 지역에서 다른 VNet을 사용 하는 경우 두 Vnet 간에 피어 링을 만들어야 합니다.
* Azure NetApp Files는 `user` 및 `resource forest` 형식을 지원 합니다.
* 동기화 유형에 대해 또는 `All` `Scoped`를 선택할 수 있습니다.   
    을 선택 `Scoped`하는 경우 SMB 공유에 액세스 하기 위해 올바른 Azure AD 그룹을 선택 해야 합니다.  확실 하지 않은 경우 `All` 동기화 유형을 사용할 수 있습니다.
* Enterprise 또는 Premium SKU를 사용 해야 합니다. 표준 SKU는 지원 되지 않습니다.

Active Directory 연결을 만들 때 다음과 같은 추가 정보를 확인 합니다.

* AADDS 메뉴에서 **기본 dns**, **보조 DNS**및 **AD DNS 도메인 이름** 에 대 한 정보를 찾을 수 있습니다.  
DNS 서버의 경우 Active Directory 연결을 구성 하는 데 두 개의 IP 주소가 사용 됩니다. 
* **조직 구성 단위 경로** 는 `OU=AADDC Computers`입니다.  
이 설정은 **Netapp Account**의 **Active Directory 연결** 에서 구성 됩니다.

  ![조직 구성 단위 경로](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **사용자 이름** 자격 증명은 azure Ad 그룹 **Azure ad DC 관리자**의 구성원 인 모든 사용자가 될 수 있습니다.


## <a name="create-an-active-directory-connection"></a>Active Directory 연결 만들기

1. NetApp 계정에서 **Active Directory 연결**을 클릭 한 다음 **조인**을 클릭 합니다.  

    ![연결 Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 조인 Active Directory 창에서 사용 하려는 도메인 서비스에 따라 다음 정보를 제공 합니다.  

    사용 하는 도메인 서비스에 대 한 자세한 내용은 [사용할 도메인 서비스 결정](#decide-which-domain-services-to-use)을 참조 하세요. 

    * **기본 DNS**  
        Active Directory 도메인 가입 및 SMB 인증 작업에 필요한 DNS입니다. 
    * **보조 DNS**   
        중복 된 이름 서비스를 보장 하기 위한 보조 DNS 서버입니다. 
    * **AD DNS 도메인 이름**  
        가입 하려는 Active Directory Domain Services의 도메인 이름입니다.
    * **AD 사이트 이름**  
        도메인 컨트롤러 검색이 제한 될 사이트 이름입니다.
    * **SMB 서버 (컴퓨터 계정) 접두사**  
        Azure NetApp Files에서 새 계정을 만드는 데 사용할 Active Directory의 컴퓨터 계정에 대 한 명명 접두사입니다.

        예를 들어 조직에서 파일 서버에 사용 하는 명명 표준이 NAS-01, NAS-02 ..., NAS-045 인 경우 접두사에 "NAS"를 입력 합니다. 

        이 서비스는 필요에 따라 Active Directory에서 추가 컴퓨터 계정을 만듭니다.

    * **조직 구성 단위 경로**  
        SMB 서버 컴퓨터 계정이 생성 되는 OU (조직 구성 단위)에 대 한 LDAP 경로입니다. 즉, OU = second level, OU = first level입니다. 

        Azure Active Directory Domain Services에서 Azure NetApp Files를 사용 하는 경우 조직 구성 단위 경로는 `OU=AADDC Computers` netapp 계정에 대 한 Active Directory를 구성 하는 경우입니다.
        
    * **사용자 이름** 및 **암호** 를 포함 한 자격 증명

    ![조인 Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **조인**을 클릭합니다.  

    만든 Active Directory 연결이 표시 됩니다.

    ![연결 Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Active Directory 연결을 저장 한 후 사용자 이름 및 암호 필드를 편집할 수 있습니다. 연결을 저장 한 후에는 다른 값을 편집할 수 없습니다. 다른 값을 변경 해야 하는 경우 먼저 배포 된 SMB 볼륨을 삭제 한 다음 Active Directory 연결을 삭제 하 고 다시 만들어야 합니다.

## <a name="add-an-smb-volume"></a>SMB 볼륨 추가

1. 용량 풀 블레이드에서 **볼륨** 블레이드를 클릭 합니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    볼륨 만들기 창이 나타납니다.

3. 볼륨 만들기 창에서 **만들기** 를 클릭 하 고 다음 필드에 대 한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 모든 영숫자 문자를 사용할 수 있습니다.   

        를 볼륨 이름 `default` 으로 사용할 수 없습니다.

    * **용량 풀**  
        볼륨을 만들 용량 풀을 지정 합니다.

    * **양보다**  
        볼륨에 할당되는 논리 스토리지의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스 하려는 Azure VNet (가상 네트워크)을 지정 합니다.  

        사용자가 지정 하는 VNet에는 Azure NetApp Files에 대해 위임 된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어 링을 통해 볼륨과 동일한 지역에 있는 VNet 에서만 액세스할 수 있습니다. Express 경로를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임 하지 않은 경우 볼륨 만들기 페이지에서 **새로 만들기** 를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp Files로 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **프로토콜** 을 클릭 하 고 다음 정보를 완료 합니다.  
    * 볼륨의 프로토콜 유형으로 **SMB** 를 선택 합니다. 
    * 드롭다운 목록에서 **Active Directory** 연결을 선택 합니다.
    * 공유 **이름**에 공유 볼륨의 이름을 지정 합니다.

    ![SMB 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **검토 + 만들기** 를 클릭 하 여 볼륨 세부 정보를 검토 합니다.  그런 다음 **만들기** 를 클릭 하 여 SMB 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시 됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [SMB Faq](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure 서비스에 대 한 가상 네트워크 통합에 대해 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure CLI를 사용 하 여 새 Active Directory 포리스트 설치](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
