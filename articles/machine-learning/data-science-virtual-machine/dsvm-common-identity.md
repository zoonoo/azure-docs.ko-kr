---
title: Data Science Virtual Machine에 대한 일반 ID 설정 - Azure | Microsoft Docs
description: 여러 Data Science Virtual Machines에 사용할 수 있는 일반 사용자 계정을 만드는 방법을 알아봅니다. Azure Active Directory 또는 온-프레미스 Active Directory를 사용하여 Data Science Virtual Machine에 사용자를 인증할 수 있습니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6be7c63d3879c7ed89cd97eaecd6d59b6b5aadd4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075474"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine에서 일반적인 ID 설정

DSVM(Data Science Virtual Machine)을 비롯한 Azure VM(가상 머신)에서 VM을 프로비전하는 동안 로컬 사용자 계정을 만듭니다. 그런 다음, 사용자는 이 자격 증명을 사용하여 VM에 인증합니다. 액세스해야 하는 여러 VM이 있는 경우 이 방법은 자격 증명을 관리하므로 번거로울 수 있습니다. 표준 기반 ID 공급자를 통해 일반적인 사용자 계정 및 관리를 사용하면 단일 자격 증명 집합을 사용하여 여러 DSVM을 포함한 Azure의 여러 리소스에 액세스할 수 있습니다. 

Active Directory는 널리 사용되는 ID 공급자이며 서비스 제공 Azure 및 온-프레미스에서 지원됩니다. Azure AD(Azure Active Directory) 또는 온-프레미스 Active Directory를 사용하여 독립 실행형 DSVM 또는 Azure 가상 머신 확장 집합의 DSVM 클러스터에서 사용자를 인증할 수 있습니다. DSVM 인스턴스를 Active Directory 도메인에 조인하여 수행합니다. 

ID를 관리하는 Active Directory가 이미 있는 경우 일반적인 ID 공급자로 사용할 수 있습니다. Active Directory가 없는 경우 Azure AD DS([Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/))라는 서비스를 통해 Azure에서 관리되는 Active Directory를 실행할 수 있습니다. 

[Azure AD](https://docs.microsoft.com/azure/active-directory/)에 대한 설명서는 있는 경우 온-프레미스 디렉터리에 Azure AD를 연결하는 방법을 포함한 자세한 [관리 지침](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity)을 제공합니다. 

이 아티클에서는 Azure AD DS를 사용하여 Azure에서 완전히 관리되는 Active Directory 도메인 서비스를 설정하는 단계를 설명합니다. 그런 다음, DSVM을 관리되는 Active Directory 도메인에 조인하여 사용자가 일반적인 사용자 계정 및 자격 증명을 사용하여 DSVM(및 다른 Azure 리소스) 풀에 액세스하도록 할 수 있습니다. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure에서 완전히 관리되는 Active Directory 도메인 설정

Azure AD DS를 사용하면 Azure에서 완전히 관리되는 서비스를 제공하여 사용자 ID를 간단하게 관리할 수 있습니다. 이 Active Directory 도메인에서 사용자 및 그룹을 관리합니다. 디렉터리에서 Azure 호스트 Active Directory 도메인 및 사용자 계정을 설정하는 단계는 다음과 같습니다.

1. Azure Portal에서 Active Directory에 사용자를 추가합니다. 

   a. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
    
   b. **Azure Active Directory**를 선택한 후 **사용자 및 그룹**을 선택합니다.
    
   다. **사용자 및 그룹**에서 **모든 사용자**를 선택한 다음 **새 사용자**를 선택합니다.
   
      **사용자** 창이 열립니다.
      
      !["사용자" 창](./media/add-user.png)
    
   d. **이름** 및 **사용자 이름**과 같은 사용자에 대한 세부 정보를 입력합니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 "[domain name].onmicrosoft.com" 또는 "contoso.com"과 같은 확인되고 페더레이션되지 않은 [사용자 지정 도메인 이름](../../active-directory/add-custom-domain.md)이어야 합니다.
    
   e. 이 프로세스가 완료된 후 사용자에게 제공할 수 있도록 복사하거나 그렇지 않은 경우 생성된 사용자 암호를 기록합니다.
    
   f. 필요에 따라 **프로필**, **그룹** 또는 사용자에 대한 **디렉터리 역할**의 정보를 열고 입력할 수 있습니다. 
    
   g. **사용자**에서 **만들기**를 선택합니다.
    
   h. 사용자가 로그인할 수 있도록 새 사용자에게 생성된 암호를 안전하게 배포합니다.

1. Azure AD DS 인스턴스를 만듭니다. [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) 아티클의 지침을 따릅니다(작업 1~작업5). Azure AD DS의 암호가 동기화되도록 Active Directory에서 기존 사용자 암호를 업데이트해야 합니다. 또한 이 아티클의 작업 4에서 설명한 대로 Azure AD DS에 DNS를 추가해야 합니다. 

1. 이전 단계의 작업 2에서 만든 가상 네트워크에 별도 DSVM 서브넷을 만듭니다.
1. DSVM 서브넷에 하나 이상의 Data Science VM 인스턴스를 만듭니다. 
1. [지침](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )에 따라 Active Directory에 DSVM을 추가합니다. 
1. 홈 또는 Notebook 디렉터리를 호스트하는 Azure Files 공유를 탑재하여 모든 컴퓨터에 작업 영역을 탑재합니다. (긴밀한 파일 수준의 사용 권한이 필요한 경우 하나 이상의 VM에서 실행되는 NFS가 필요합니다.)

   a. [Azure Files 공유 만들기](../../storage/files/storage-how-to-create-file-share.md)
    
   b. Linux DSVM에 탑재합니다. Azure Portal의 저장소 계정에서 Azure Files 공유에 대해 **연결** 단추를 선택하면 Linux DSVM의 bash 셸에서 실행할 명령이 나타납니다. 이 명령은 다음과 같습니다.
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 예를 들어 /data/workspace에 Azure Files 공유를 탑재했다고 가정합니다. 이제 공유에서 사용자 각각에 대한 디렉터리를 만듭니다(/data/workspace/user1, /data/workspace/user2 등). 각 사용자의 작업 영역에 `notebooks` 디렉터리를 만듭니다. 
1. `$HOME/userx/notebooks/remote`에서 `notebooks`에 대한 기호 링크를 만듭니다.   

이제 Azure에서 호스트되는 Active Directory 인스턴스에 사용자가 생성됩니다. 사용자는 Active Directory 자격 증명을 사용하여 Azure AD DS에 조인된 모든 DSVM(SSH 또는 JupyterHub)에 로그인할 수 있습니다. 사용자 작업 영역이 Azure Files 공유에 위치하기 때문에 사용자가 JupyterHub를 사용하는 경우 모든 DSVM에서 Notebook 및 기타 작업에 대한 액세스 권한을 갖습니다. 

자동 크기 조정의 경우 가상 머신 확장 집합을 사용하여 이러한 방법으로 탑재된 공유 디스크를 통해 도메인에 모두 조인된 VM의 풀을 만들 수 있습니다. 사용자는 가상 머신 확장 집합의 모든 사용 가능한 컴퓨터에 로그인하고 Notebook이 저장되는 공유 디스크에 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)



