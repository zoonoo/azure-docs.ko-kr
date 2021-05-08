---
title: 공통 ID 설정
titleSuffix: Azure Data Science Virtual Machine
description: 여러 Data Science Virtual Machines에 사용할 수 있는 일반 사용자 계정을 만드는 방법을 알아봅니다. Azure Active Directory 또는 온-프레미스 Active Directory를 사용하여 Data Science Virtual Machine에 사용자를 인증할 수 있습니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519731"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Data Science Virtual Machine에서 공통 ID 설정

DSVM(Data Science Virtual Machine)을 비롯한 Microsoft Azure VM(가상 머신)에서 VM을 프로비저닝하는 동안 로컬 사용자 계정을 만듭니다. 그런 다음, 사용자는 이 자격 증명을 사용하여 VM에 인증합니다. 사용자가 액세스해야 하는 여러 VM이 있는 경우 자격 증명을 관리하는 작업이 매우 복잡할 수 있습니다. 뛰어난 솔루션은 표준 기반 ID 공급자를 통해 공통 사용자 계정 및 관리를 배포하는 것입니다. 이 방법을 통해 단일 자격 증명 집합을 사용하여 여러 DSVM을 포함한 Azure의 여러 리소스에 액세스할 수 있습니다.

Active Directory는 널리 사용되는 ID 공급자로, Azure 상에서 클라우드 서비스 및 온-프레미스 디렉터리로 지원됩니다. Azure AD(Azure Active Directory) 또는 온-프레미스 Active Directory를 사용하여 독립 실행형 DSVM 또는 Azure 가상 머신 확장 집합의 DSVM 클러스터에서 사용자를 인증할 수 있습니다. DSVM 인스턴스를 Active Directory 도메인에 조인하여 수행합니다.

Active Directory가 이미 있는 경우 그것을 공통 ID 공급자로 사용할 수 있습니다. Active Directory가 없는 경우 Azure AD DS([Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml))를 통해 Azure에서 관리되는 Active Directory 인스턴스를 실행할 수 있습니다.

[Azure AD](../../active-directory/index.yml)용 설명서는 온-프레미스 디렉터리(있는 경우)에 Azure AD를 연결하기 위한 참고 자료를 포함하여 자세한 [관리 지침](../../active-directory/hybrid/whatis-hybrid-identity.md)을 제공합니다.

이 문서는 Azure AD DS를 사용하여 Azure에서 완전히 관리되는 Active Directory 도메인 서비스를 설정하는 방법을 설명합니다. 그런 다음 DSVM을 관리되는 Active Directory 도메인에 조인시킬 수 있습니다. 이 방법을 통해 사용자는 공통 사용자 계정 및 자격 증명을 통해 DSVM(및 기타 Azure 리소스) 풀에 액세스할 수 있습니다.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure에서 완전히 관리되는 Active Directory 도메인 설정

Azure AD DS를 사용하면 Azure에서 완전히 관리되는 서비스를 제공하여 사용자 ID를 간단하게 관리할 수 있습니다. 이 Active Directory 도메인에서 사용자 및 그룹을 관리합니다. Azure에서 호스트되는 Active Directory 도메인 및 사용자 계정을 디렉터리에 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Active Directory에 사용자를 추가합니다. 

   1. 디렉터리에 대한 전역 관리자 계정을 사용하여 [Azure Active Directory 관리자 센터](https://aad.portal.azure.com)에 로그인합니다.
    
   1. **Azure Active Directory** 를 선택한 후 **사용자 및 그룹** 을 선택합니다.
    
   1. **사용자 및 그룹** 에서 **모든 사용자** 로 이동한 다음 **새 사용자** 를 선택합니다.
   
        다음과 같이 **사용자** 창이 열립니다.
      
        !["사용자" 창](./media/add-user.png)
    
   1. **이름** 및 **사용자 이름** 과 같은 사용자에 대한 세부 정보를 입력합니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 "[domain name].onmicrosoft.com" 또는 "contoso.com"과 같은 확인되고 페더레이션되지 않은 [사용자 지정 도메인 이름](../../active-directory/fundamentals/add-custom-domain.md)이어야 합니다.
    
   1. 이 프로세스가 완료된 후 사용자에게 제공할 수 있도록 복사하거나 그렇지 않은 경우 생성된 사용자 암호를 기록합니다.
    
   1. 필요에 따라 **프로필**, **그룹** 또는 사용자에 대한 **디렉터리 역할** 의 정보를 열고 입력할 수 있습니다. 
    
   1. **사용자** 에서 **만들기** 를 선택합니다.
    
   1. 새 사용자가 로그인할 수 있도록 생성된 암호를 안전하게 배포합니다.

1. Azure AD DS 인스턴스를 만듭니다. [Azure Portal을 사용하여 Azure Active Directory Domain Services 사용](../../active-directory-domain-services/tutorial-create-instance.md)(“인스턴스 만들기 및 기본 설정 구성” 섹션)의 지침을 따르세요. Azure AD DS의 암호가 동기화되도록 Active Directory에서 기존 사용자 암호를 업데이트해야 합니다. 또한 해당 섹션의 “Azure AD DS 인스턴스를 만들기 위해 Azure Portal의 기본 사항 창에서 필드 완료” 아래에 설명된 대로 Azure AD DS에 DNS를 추가하는 것이 중요합니다.

1. 이전 단계의 “가상 네트워크 만들기 및 구성” 섹션에서 만든 가상 네트워크에 별도의 DSVM 서브넷을 만듭니다.
1. DSVM 서브넷에서 하나 이상의 DSVM 인스턴스를 만듭니다.
1. [지침](../../active-directory-domain-services/join-ubuntu-linux-vm.md)에 따라 Active Directory에 DSVM을 추가합니다. 
1. 홈 또는 Notebook 디렉터리를 호스트하기 위해 Azure Files 공유를 탑재하여, 모든 컴퓨터에 작업 영역이 탑재될 수 있게 합니다. (근접한 파일 수준 사용 권한이 필요한 경우 하나 이상의 VM에서 NFS[네트워크 파일 시스템]를 실행해야 합니다.)

   1. [Azure Files 공유 만들기](../../storage/files/storage-how-to-create-file-share.md)
    
   2.  해당 공유를 Linux DSVM에 탑재합니다. Azure Portal의 스토리지 계정에서 Azure Files 공유에 대해 **연결** 을 선택하면 Linux DSVM의 Bash 셸에서 실행할 명령이 나타납니다. 이 명령은 다음과 같습니다.
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 예를 들어 /data/workspace에 Azure Files 공유를 탑재했다고 가정합니다. 이제 공유에서 /data/workspace/user1, /data/workspace/user2 등과 같이 사용자 각각에 대한 디렉터리를 만듭니다. 각 사용자의 작업 영역에 `notebooks` 디렉터리를 만듭니다. 
1. `$HOME/userx/notebooks/remote`에서 `notebooks`에 대한 기호 링크를 만듭니다.   

이제 Azure에서 호스트되는 Active Directory 인스턴스에 사용자가 있습니다. 사용자는 Active Directory 자격 증명을 사용하여 Azure AD DS에 조인된 모든 DSVM(SSH 또는 JupyterHub)에 로그인할 수 있습니다. 사용자 작업 영역이 Azure Files 공유에 위치하기 때문에 사용자가 JupyterHub를 사용하는 경우 모든 DSVM에서 Notebook 및 기타 작업에 대한 액세스 권한을 갖습니다.

자동 크기 조정의 경우 가상 머신 확장 집합을 사용하여 이러한 방법으로 탑재된 공유 디스크를 통해 도메인에 모두 조인된 VM의 풀을 만들 수 있습니다. 사용자는 Virtual Machine Scale Sets의 모든 사용 가능한 컴퓨터에 로그인하고 Notebook이 저장되는 공유 디스크에 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)