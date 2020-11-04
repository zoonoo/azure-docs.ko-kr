---
title: 공통 id 설정
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
ms.openlocfilehash: 3dc6fb64f6e8695d84e292322293998e2f4cb0a3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324779"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Data Science Virtual Machine에서 공통 id 설정

Data Science Virtual Machine (DSVM)를 비롯 한 Microsoft Azure VM (가상 머신)에서 VM을 프로 비전 하는 동안 로컬 사용자 계정을 만듭니다. 그런 다음, 사용자는 이 자격 증명을 사용하여 VM에 인증합니다. 사용자가 액세스 해야 하는 여러 Vm이 있는 경우 자격 증명을 관리 하는 작업이 매우 복잡할 수 있습니다. 뛰어난 솔루션은 표준 기반 id 공급자를 통해 일반 사용자 계정 및 관리를 배포 하는 것입니다. 이 접근 방식을 통해 단일 자격 증명 집합을 사용 하 여 여러 DSVMs을 포함 하 여 Azure의 여러 리소스에 액세스할 수 있습니다.

Active Directory는 널리 사용 되는 id 공급자 이며, 클라우드 서비스와 온-프레미스 디렉터리로 Azure에서 지원 됩니다. Azure AD(Azure Active Directory) 또는 온-프레미스 Active Directory를 사용하여 독립 실행형 DSVM 또는 Azure 가상 머신 확장 집합의 DSVM 클러스터에서 사용자를 인증할 수 있습니다. DSVM 인스턴스를 Active Directory 도메인에 조인하여 수행합니다.

Active Directory 이미 있는 경우이를 일반적인 id 공급자로 사용할 수 있습니다. Active Directory 없는 경우 [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (azure AD DS)를 통해 azure에서 관리 되는 Active Directory 인스턴스를 실행할 수 있습니다.

Azure [ad](../../active-directory/index.yml) 에 대 한 설명서는 azure ad를 온-프레미스 디렉터리 (있는 경우)에 연결 하는 방법에 대 한 지침을 포함 하 여 자세한 [관리 지침](../../active-directory/hybrid/whatis-hybrid-identity.md)을 제공 합니다.

이 문서에서는 azure AD DS를 사용 하 여 Azure에서 완전히 관리 되는 Active Directory 도메인 서비스를 설정 하는 방법을 설명 합니다. 그런 다음 DSVMs을 관리 되는 Active Directory 도메인에 조인할 수 있습니다. 이 접근 방식을 통해 사용자는 일반 사용자 계정 및 자격 증명을 통해 DSVMs (및 기타 Azure 리소스) 풀에 액세스할 수 있습니다.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure에서 완전히 관리되는 Active Directory 도메인 설정

Azure AD DS를 사용하면 Azure에서 완전히 관리되는 서비스를 제공하여 사용자 ID를 간단하게 관리할 수 있습니다. 이 Active Directory 도메인에서 사용자 및 그룹을 관리합니다. Azure에서 호스트 되는 Active Directory 도메인 및 사용자 계정을 디렉터리에 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Active Directory에 사용자를 추가합니다. 

   1. 디렉터리에 대 한 전역 관리자 인 계정을 사용 하 여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
    
   1. **Azure Active Directory** 를 선택한 후 **사용자 및 그룹** 을 선택합니다.
    
   1. **사용자 및 그룹** 에서 **모든 사용자** 를 선택한 다음 **새 사용자** 를 선택 합니다.
   
        **사용자** 창이 열립니다.
      
        !["사용자" 창](./media/add-user.png)
    
   1. **이름** 및 **사용자 이름** 과 같은 사용자에 대한 세부 정보를 입력합니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 "[domain name].onmicrosoft.com" 또는 "contoso.com"과 같은 확인되고 페더레이션되지 않은 [사용자 지정 도메인 이름](../../active-directory/fundamentals/add-custom-domain.md)이어야 합니다.
    
   1. 이 프로세스가 완료된 후 사용자에게 제공할 수 있도록 복사하거나 그렇지 않은 경우 생성된 사용자 암호를 기록합니다.
    
   1. 필요에 따라 **프로필** , **그룹** 또는 사용자에 대한 **디렉터리 역할** 의 정보를 열고 입력할 수 있습니다. 
    
   1. **사용자** 에서 **만들기** 를 선택 합니다.
    
   1. 생성 된 암호를 새 사용자에 게 안전 하 게 배포 하 여 로그인 할 수 있도록 합니다.

1. Azure AD DS 인스턴스를 만듭니다. [Azure Portal를 사용 하 여 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) 사용 ("인스턴스 만들기 및 기본 설정 구성" 섹션)의 지침을 따르세요. Azure AD DS의 암호가 동기화되도록 Active Directory에서 기존 사용자 암호를 업데이트해야 합니다. 또한이 섹션의 "Azure AD DS 인스턴스를 만들려면 Azure Portal의 기본 사항 창에서 필드 완료"에 설명 된 대로 Azure AD DS에 DNS를 추가 하는 것이 중요 합니다.

1. 이전 단계의 "가상 네트워크 만들기 및 구성" 섹션에서 만든 가상 네트워크에 별도의 DSVM 서브넷을 만듭니다.
1. DSVM 서브넷에서 DSVM 인스턴스를 하나 이상 만듭니다.
1. [지침](../../active-directory-domain-services/join-ubuntu-linux-vm.md) 에 따라 dsvm을 Active Directory에 추가 합니다. 
1. 작업 영역을 모든 컴퓨터에 탑재할 수 있도록 홈 또는 노트북 디렉터리를 호스트 하는 Azure Files 공유를 탑재 합니다. (근접 한 파일 수준 사용 권한이 필요한 경우 하나 이상의 Vm에서 NFS (네트워크 파일 시스템)를 실행 해야 합니다.)

   1. [Azure Files 공유 만들기](../../storage/files/storage-how-to-create-file-share.md)
    
   2.  Linux DSVM에이 공유를 탑재 합니다. Azure Portal의 저장소 계정에서 Azure Files 공유에 대 한 **연결** 을 선택 하면 LINUX dsvm의 bash 셸에서 실행 하는 명령이 표시 됩니다. 이 명령은 다음과 같습니다.
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 예를 들어/data/workspace.에 Azure Files 공유를 탑재 했다고 가정 합니다. 이제 공유에서 각 사용자에 대 한 디렉터리 (/data/workspace/user1,/data/workspace/user2 등)를 만듭니다. 각 사용자의 작업 영역에 `notebooks` 디렉터리를 만듭니다. 
1. `$HOME/userx/notebooks/remote`에서 `notebooks`에 대한 기호 링크를 만듭니다.   

이제 Azure에서 호스트 되는 Active Directory 인스턴스에 사용자가 있습니다. 사용자는 Active Directory 자격 증명을 사용 하 여 Azure AD DS에 조인 된 모든 DSVM (SSH 또는 JupyterHub)에 로그인 할 수 있습니다. 사용자 작업 영역이 Azure Files 공유에 위치하기 때문에 사용자가 JupyterHub를 사용하는 경우 모든 DSVM에서 Notebook 및 기타 작업에 대한 액세스 권한을 갖습니다.

자동 크기 조정의 경우 가상 머신 확장 집합을 사용하여 이러한 방법으로 탑재된 공유 디스크를 통해 도메인에 모두 조인된 VM의 풀을 만들 수 있습니다. 사용자는 가상 머신 확장 집합에서 사용 가능한 모든 컴퓨터에 로그인 하 고 해당 노트북이 저장 된 공유 디스크에 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)