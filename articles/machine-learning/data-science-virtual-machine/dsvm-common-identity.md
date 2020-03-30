---
title: 공통 ID 설정
titleSuffix: Azure Data Science Virtual Machine
description: 여러 Data Science Virtual Machines에 사용할 수 있는 일반 사용자 계정을 만드는 방법을 알아봅니다. Azure Active Directory 또는 온-프레미스 Active Directory를 사용하여 Data Science Virtual Machine에 사용자를 인증할 수 있습니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208153"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>데이터 과학 가상 머신에서 공통 ID 설정

데이터 과학 가상 머신(DSVM)을 포함한 Microsoft Azure 가상 시스템(VM)에서 VM을 프로비전하는 동안 로컬 사용자 계정을 만듭니다. 그런 다음, 사용자는 이 자격 증명을 사용하여 VM에 인증합니다. 사용자가 액세스해야 하는 VM이 여러 개인 경우 자격 증명을 관리하는 것이 매우 번거로울 수 있습니다. 훌륭한 솔루션은 표준 기반 ID 공급자를 통해 일반적인 사용자 계정 및 관리를 배포하는 것입니다. 이 방법을 통해 단일 자격 증명 집합을 사용하여 여러 DSVM을 포함하여 Azure의 여러 리소스에 액세스할 수 있습니다.

Active Directory는 인기 있는 ID 공급자이며 Azure에서 클라우드 서비스 및 온-프레미스 디렉터리로 지원됩니다. Azure AD(Azure Active Directory) 또는 온-프레미스 Active Directory를 사용하여 독립 실행형 DSVM 또는 Azure 가상 머신 확장 집합의 DSVM 클러스터에서 사용자를 인증할 수 있습니다. DSVM 인스턴스를 Active Directory 도메인에 조인하여 수행합니다.

Active Directory가 이미 있는 경우 공통 ID 공급자로 사용할 수 있습니다. Active Directory가 없는 경우 Azure [Active Directory 도메인 서비스(Azure](https://docs.microsoft.com/azure/active-directory-domain-services/) AD DS)를 통해 Azure에서 관리되는 Active Directory 인스턴스를 실행할 수 있습니다.

[Azure AD에](https://docs.microsoft.com/azure/active-directory/) 대한 설명서는 Azure AD를 온-프레미스 디렉터리에 연결하는 방법에 대한 지침을 포함하여 자세한 [관리 지침을](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)제공합니다.

이 문서에서는 Azure AD DS를 사용하여 Azure에서 완전히 관리되는 Active Directory 도메인 서비스를 설정하는 방법에 대해 설명합니다. 그런 다음 DSVM을 관리되는 Active Directory 도메인에 가입할 수 있습니다. 이 방법을 사용하면 사용자가 공통 사용자 계정 및 자격 증명을 통해 DSVM(및 기타 Azure 리소스) 풀에 액세스할 수 있습니다.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure에서 완전히 관리되는 Active Directory 도메인 설정

Azure AD DS를 사용하면 Azure에서 완전히 관리되는 서비스를 제공하여 사용자 ID를 간단하게 관리할 수 있습니다. 이 Active Directory 도메인에서 사용자 및 그룹을 관리합니다. 디렉터리에서 Azure 호스팅 Active Directory 도메인 및 사용자 계정을 설정하려면 다음 단계를 따르십시오.

1. Azure Portal에서 Active Directory에 사용자를 추가합니다. 

   1. 디렉터리의 전역 관리자인 계정을 사용하여 [Azure Active Directory 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
    
   1. **Azure Active Directory**를 선택한 후 **사용자 및 그룹**을 선택합니다.
    
   1. **사용자 및 그룹에서** **모든 사용자를**선택한 다음 새 **사용자를**선택합니다.
   
           The **User** pane opens:
      
      !["사용자" 창](./media/add-user.png)
    
   1. **이름** 및 **사용자 이름**과 같은 사용자에 대한 세부 정보를 입력합니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 "[domain name].onmicrosoft.com" 또는 "contoso.com"과 같은 확인되고 페더레이션되지 않은 [사용자 지정 도메인 이름](../../active-directory/add-custom-domain.md)이어야 합니다.
    
   1. 이 프로세스가 완료된 후 사용자에게 제공할 수 있도록 복사하거나 그렇지 않은 경우 생성된 사용자 암호를 기록합니다.
    
   1. 필요에 따라 **프로필**, **그룹** 또는 사용자에 대한 **디렉터리 역할**의 정보를 열고 입력할 수 있습니다. 
    
   1. **사용자**에서 을 **선택합니다.**
    
   1. 생성된 암호를 새 사용자에게 안전하게 배포하여 로그인할 수 있습니다.

1. Azure AD DS 인스턴스를 만듭니다. [Azure 포털을 사용하여 Azure Active Directory 도메인 서비스 사용의](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) 지침을 따릅니다("인스턴스 만들기 및 기본 설정 구성" 섹션). Azure AD DS의 암호가 동기화되도록 Active Directory에서 기존 사용자 암호를 업데이트해야 합니다. 해당 섹션에서 "Azure AD DS 인스턴스를 만들기 위해 Azure 포털의 기본 창에서 필드를 완료"에 설명된 대로 Azure AD DS에 DNS를 추가하는 것도 중요합니다.

1. 이전 단계의 "가상 네트워크 만들기 및 구성" 섹션에서 만든 가상 네트워크에서 별도의 DSVM 서브넷을 만듭니다.
1. DSVM 서브넷에서 하나 이상의 DSVM 인스턴스를 만듭니다.
1. [지침에](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) 따라 DSVM을 Active Directory에 추가합니다. 
1. Azure Files 공유를 마운트하여 홈 또는 노트북 디렉토리를 호스트하여 모든 컴퓨터에 작업 공간을 탑재할 수 있습니다. (엄격한 파일 수준 권한이 필요한 경우 하나 이상의 VM에서 실행되는 네트워크 파일 시스템[NFS])이 필요합니다.)

   1. [Azure Files 공유 만들기](../../storage/files/storage-how-to-create-file-share.md)
    
   2.  리눅스 DSVM에이 공유를 탑재. Azure 포털의 저장소 계정에서 Azure 파일 공유에 대한 **연결을** 선택하면 Linux DSVM의 bash 셸에서 실행할 명령이 나타납니다. 이 명령은 다음과 같습니다.
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 예를 들어 Azure Files 공유를 /data/작업 공간에 탑재했다고 가정합니다. 이제 공유의 각 사용자(/데이터/작업 영역/user1, /data/workspace/user2 등)에 대한 디렉터리를 만듭니다. 각 사용자의 작업 영역에 `notebooks` 디렉터리를 만듭니다. 
1. `$HOME/userx/notebooks/remote`에서 `notebooks`에 대한 기호 링크를 만듭니다.   

이제 Active Directory 인스턴스의 사용자가 Azure에서 호스팅됩니다. Active Directory 자격 증명을 사용하여 사용자는 Azure AD DS에 조인된 모든 DSVM(SSH 또는 JupyterHub)에 로그인할 수 있습니다. 사용자 작업 영역이 Azure Files 공유에 위치하기 때문에 사용자가 JupyterHub를 사용하는 경우 모든 DSVM에서 Notebook 및 기타 작업에 대한 액세스 권한을 갖습니다.

자동 크기 조정의 경우 가상 머신 확장 집합을 사용하여 이러한 방법으로 탑재된 공유 디스크를 통해 도메인에 모두 조인된 VM의 풀을 만들 수 있습니다. 사용자는 가상 시스템 크기 집합에서 사용 가능한 모든 컴퓨터에 로그인하고 노트북이 저장된 공유 디스크에 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)



