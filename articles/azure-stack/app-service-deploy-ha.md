---
title: 항상 사용 가능한 구성에서 Azure Stack의 App Service 배포 | Microsoft Docs
description: 항상 사용 가능한 구성을 사용 하 여 Azure Stack에서 App Service를 배포 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/13/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: db95be94028fcf16871a9dcfee5f0d87eb5d2cdc
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285669"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>항상 사용 가능한 구성에서 App Service 배포

이 문서에서는 Azure Stack marketplace 항목을 사용 하 여 항상 사용 가능한 구성에서 Azure Stack에 대 한 App Service를 배포 하는 방법을 보여 줍니다. 사용할 수 있는 marketplace 항목 외에도이 솔루션도 사용 합니다 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 빠른 시작 템플릿. 이 템플릿은 호스팅 App Service 리소스 공급자에 대 한 고가용성 인프라 만들기를 자동화 합니다. App Service가이 항상 사용 가능한 VM 인프라에 대해 설치 됩니다. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>항상 사용 가능한 App Service 인프라 Vm 배포
합니다 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) 고가용성 구성에서 App Service의 배포를 간소화 하는 Azure Stack 빠른 시작 템플릿. 기본 공급자 구독에 배포 해야 합니다. 

Azure Stack에 사용자 지정 리소스를 만드는 데, 템플릿을 만듭니다.
- 가상 네트워크 및 서브넷 필요 합니다.
- 파일 서버, SQL Server 및 Active Directory Domain Services (AD DS) 서브넷에 대 한 네트워크 보안 그룹입니다.
- VM 디스크 및 클러스터 클라우드 감시에 대 한 저장소 계정입니다.
- SQL AlwaysOn 수신기를 바인딩할 개인 IP 사용 하 여 SQL Vm에 대 한 하나의 내부 부하 분산 합니다.
- AD DS, 파일 서버 클러스터 및 SQL 클러스터에 대 한 세 개의 가용성 집합.
- 두 노드 SQL 클러스터입니다.
- 두 노드 파일 서버 클러스터입니다.
- 두 개의 도메인 컨트롤러입니다.

### <a name="required-azure-stack-marketplace-items"></a>필요한 Azure Stack marketplace 항목
이 템플릿을 사용 하기 전에 확인 하는 다음 [Azure Stack marketplace 항목](azure-stack-marketplace-azure-items.md) Azure Stack 인스턴스에 사용할 수 있습니다.

- (AD DS 및 파일 서버 Vm)에 대 한 Windows Server 2016 Datacenter Core 이미지
- Windows Server 2016 (Enterprise)의 SQL Server 2016 SP2
- 최신 SQL IaaS 확장 
- 최신 PowerShell Desired State Configuration 확장 

> [!TIP]
> 검토 [템플릿 추가 정보 파일](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) 템플릿 요구 사항 및 기본 값에 대 한 자세한 내용은 GitHub의 합니다. 

### <a name="deploy-the-app-service-infrastructure"></a>App Service 인프라를 배포 합니다.
사용 하 여 사용자 지정 배포를 만들려면이 섹션의 단계를 사용 합니다 **appservice-fileshare-sqlserver-ha** Azure Stack 빠른 시작 템플릿.

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 선택 **\+** **리소스 만들기** > **Custom**를 차례로 **템플릿 배포**.

   ![사용자 지정 템플릿 배포](media/app-service-deploy-ha/1.png)


3. 에 **사용자 지정 배포** 블레이드에서 **템플릿 편집** > **빠른 시작 템플릿** 다음에 사용 가능한 사용자 지정 템플릿 드롭다운 목록을 사용 하 여 선택 된 **appservice-fileshare-sqlserver-ha** 템플릿을 클릭 **확인**를 차례로 **저장**.

   ![Appservice-fileshare-sqlserver-ha 빠른 시작 템플릿 선택](media/app-service-deploy-ha/2.png)

4. 에 **사용자 지정 배포** 블레이드에서 **매개 변수 편집** 템플릿 기본값을 검토까지 아래로 스크롤합니다. 모든 필수 매개 변수 정보를 제공 하 고 클릭 한 다음 필요에 따라 이러한 값을 수정 **확인**합니다.<br><br> 최소한 ADMINPASSWORD "," FILESHAREOWNERPASSWORD "," FILESHAREUSERPASSWORD "," SQLSERVERSERVICEACCOUNTPASSWORD, "및" SQLLOGINPASSWORD 매개 변수에 대 한 복잡 한 암호를 제공 합니다.
    
   ![사용자 지정 배포 매개 변수 편집](media/app-service-deploy-ha/3.png)

5. 에 **사용자 지정 배포** 블레이드에서 확인 **공급자 구독 기본** 를 사용 하 여 다음 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용자 지정에 대 한 선택에 대 한 구독이 선택 배포 합니다.<br><br> 다음으로, 리소스 그룹 위치를 선택 (**로컬** ASDK 설치의 경우)을 클릭 한 다음 **만들기**합니다. 템플릿 배포를 시작 하기 전에 사용자 지정 배포 설정은 유효성을 검사 합니다.

    ![사용자 지정 배포 만들기](media/app-service-deploy-ha/4.png)

6. 관리 포털에서 선택 **리소스 그룹** 한 다음 리소스 그룹의 이름에 대해 만든 사용자 지정 배포 및 (**앱-서비스-ha** 이 예제의). 모든 배포가 성공적으로 완료 되도록 배포의 상태를 봅니다.

   > [!NOTE]
   > 템플릿 배포를 완료 한 시간에 대 한 걸립니다.

   [![](media/app-service-deploy-ha/5-sm.png "템플릿 배포 상태를 검토 합니다.")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>레코드 템플릿 출력
템플릿을 배포가 완료 된 후 성공적으로 레코드 템플릿 배포를 출력 합니다. App Service 설치 관리자를 실행 하는 경우이 정보를 제공 해야 합니다. 

이러한 각 출력 값을 레코드를 확인 합니다.
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

템플릿 출력 값을 검색 하려면 다음이 단계를 수행 합니다.

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 관리 포털에서 선택 **리소스 그룹** 한 다음 리소스 그룹의 이름에 대해 만든 사용자 지정 배포 및 (**앱-서비스-ha** 이 예제의). 

3. 클릭 **배포** 선택한 **Microsoft.Template**합니다.

    ![Microsoft.Template 배포](media/app-service-deploy-ha/6.png)

4. 선택한 후는 **Microsoft.Template** 배포 선택 **출력** 템플릿 매개 변수 출력을 기록 합니다. 이 정보는 App Service를 배포 하는 경우 필요한 수 있습니다.

    ![매개 변수 출력](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>항상 사용 가능한 구성에서 App Service 배포
항상 사용 가능한 구성 기준에서 Azure Stack에 대 한 App Service를 배포 하려면이 섹션의 단계를 수행 합니다 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 빠른 시작 템플릿. 

App Service 리소스 공급자를 설치한 후에 제품 및 계획에 포함할 수 있습니다. 사용자 서비스 및 응용 프로그램을 만들기 시작 하려면 다음 구독할 수 있습니다.

> [!IMPORTANT]
> 리소스 공급자 설치 관리자를 실행 하기 전에 새로운 기능, 수정 및 배포에 영향을 줄 수 있는 알려진된 문제에 대해 자세히 알아보려면 각 App Service 출시와 함께 제공 되는 릴리스 정보를 읽었다고 있는지 확인 합니다.

### <a name="prerequisites"></a>필수 조건
몇 가지 단계가 필요에 설명 된 대로 App Service 설치 관리자를 실행 하기 전에 합니다 [문서에서는 Azure Stack에서 App Service를 사용 하 여 시작 하기 전에](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> 에 설명 된 일부 단계는 템플릿 배포를 Vm 인프라를 구성 하기 때문에 문서는 필수 시작 하기 전에 합니다. 

- [App Service 설치 관리자 및 도우미 스크립트 다운로드](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)합니다.
- [Azure Stack marketplace에 최신 사용자 지정 스크립트 확장을 다운로드할](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)합니다.
- [필요한 인증서를 생성할](azure-stack-app-service-before-you-get-started.md#get-certificates)합니다.
- Azure Stack에 대 한 선택한 id 공급자를 기준으로 ID 응용 프로그램을 만듭니다. 에 대 한 ID 응용 프로그램을 만들 수 있습니다 [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) 하거나 [Active Directory Federation Services](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) 응용 프로그램 ID를 기록 하 고
- Azure Stack marketplace에 Windows Server 2016 Datacenter 이미지를 추가 했다고 확인 합니다. App Service 설치에 대 한 필수입니다.

### <a name="deploy-app-service-in-highly-available-configuration"></a>항상 사용 가능한 구성에서 App Service 배포
1 시간 이상는 App Service 리소스 공급자를 설치 합니다. 얼마나 많은 역할 인스턴스 수에 따라 달라 집니다 필요한 시간의 길이 배포 합니다. 배포 하는 동안 설치 관리자는 다음 작업을 실행 합니다.

- 지정된 된 Azure Stack 저장소 계정에서 blob 컨테이너를 만듭니다.
- App Service에 대 한 DNS 영역 및 항목을 만듭니다.
- App Service 리소스 공급자를 등록 합니다.
- App Service 갤러리 항목을 등록 합니다.

App Service 리소스 공급자를 배포 하려면 다음이 단계를 수행 합니다.

1. 이전에 다운로드 한 앱 서비스 설치 관리자 실행 (**appservice.exe**) Azure Stack 관리자 Azure 리소스 관리 끝점에 액세스할 수 있는 컴퓨터에서 관리자 권한으로 합니다.

2. 선택 **App Service 배포 또는 최신 버전으로 업그레이드**합니다.

    ![배포 또는 업그레이드](media/app-service-deploy-ha/01.png)

3. Microsoft 사용 조건에 동의 하 고 클릭 **다음**합니다.

    ![Microsoft 라이선스 조건](media/app-service-deploy-ha/02.png)

4. 타사 라이선스 조건에 동의 하 고 클릭 **다음**합니다.

    ![타사 라이선스 조건](media/app-service-deploy-ha/03.png)

5. App Service를 Azure Stack 환경에 클라우드 끝점 구성을 제공 합니다.

    ![App Service 클라우드 끝점 구성](media/app-service-deploy-ha/04.png)

6. **연결** 설치에 사용할 위치를 선택 하는 Azure Stack 구독에 있습니다. 

    ![Azure Stack 구독에 연결](media/app-service-deploy-ha/05.png)

7. 선택 **기존 VNet 및 서브넷을 사용 하 여** 하며 **리소스 그룹 이름을** 항상 사용 가능한 템플릿을 배포 하는 데 사용 되는 리소스 그룹에 대 한 합니다.<br><br>다음으로, 템플릿 배포의 일부로 만든 가상 네트워크를 선택 하 고 드롭다운 목록에서 옵션 중에서 적절 한 역할 서브넷을 선택 합니다. 

    ![Vnet 선택](media/app-service-deploy-ha/06.png)

8. 이전에 기록 된 템플릿의 출력 파일 공유 경로 파일 공유 소유자 매개 변수 정보를 제공 합니다. 완료되면 **다음**을 클릭합니다.

    ![출력 정보를 공유 하는 파일](media/app-service-deploy-ha/07.png)

9. App Service를 설치 하는 데 사용 되는 컴퓨터는 App Service 파일 공유를 호스트 하는 데 사용 되는 파일 서버와 동일한 VNet에 위치 하지 않은, 때문에 이름을 확인할 수 없습니다. 이는 정상적인 동작입니다.<br><br>파일 공유 UNC 경로 및 계정 정보에 대 한 입력 한 정보가 올바른지 확인 하 고 키를 누릅니다 **예** App Service 설치를 계속 하려면 경고 대화 상자에서.

    ![예상 되는 오류 대화 상자](media/app-service-deploy-ha/08.png)

10. 경로 Id 응용 프로그램 ID 및 암호를 id 인증서를 제공 하 고 클릭 **다음**:
    - Id 응용 프로그램 인증서 (형식의 **sso.appservice.local.azurestack.external.pfx**)
    - Azure 리소스 관리자 루트 인증서 (**AzureStackCertificationAuthority.cer**)

    ![ID 응용 프로그램 인증서 및 루트 인증서](media/app-service-deploy-ha/008.png)

10. 다음으로, 다음 인증서에 필요한 나머지 정보를 제공 하 고 클릭 **다음**:
    - 기본 Azure Stack SSL 인증서 (형식의 **_.appservice.local.azurestack.external.pfx**)
    - API SSL 인증서 (형식의 **api.appservice.local.azurestack.external.pfx**)
    - 게시자 인증서 (형태로 **ftp.appservice.local.azurestack.external.pfx**) 

    ![추가 구성 인증서](media/app-service-deploy-ha/09.png)

11. 고가용성 템플릿 배포 출력에서 SQL Server 연결 정보를 사용 하 여 SQL Server 연결 정보를 제공 합니다.

    ![SQL Server 연결 정보](media/app-service-deploy-ha/10.png)

12. App Service를 설치 하는 데 사용 되는 컴퓨터는 App Service 데이터베이스를 호스트 하는 데 사용 되는 SQL server와 동일한 VNet에 위치 하지 않은, 때문에 이름을 확인할 수 없습니다.  이는 정상적인 동작입니다.<br><br>SQL Server 이름 및 계정 정보에 대해 입력 한 정보가 올바른지 확인 하 고 키를 눌러 **예** App Service 설치를 계속 합니다. **다음**을 클릭합니다.

    ![SQL Server 연결 정보](media/app-service-deploy-ha/11.png)

13. 기본 역할 구성 값을 수락 하거나 권장 되는 값으로 변경 하 고, 클릭 **다음**합니다.<br><br>항상 사용 가능한 구성에 대 한 App Service 인프라 역할 인스턴스에 대 한 기본값을 다음과 같이 변경 하는 것이 좋습니다.

    |역할|기본값|항상 사용 가능한 권장 사항|
    |-----|-----|-----|
    |컨트롤러 역할|2|2|
    |관리 역할|1|3|
    |게시자 역할|1|3|
    |프런트 엔드 역할|1|3|
    |공유 작업자 역할|1|10|
    |     |     |     |

    ![인프라 역할 인스턴스 값](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 이러한 기본값을 변경 권장 tutoral 증가이 App Service를 설치 하기 위한 하드웨어 요구 사항. 총 26 개 코어 및 ram 46,592 MB 15 Vm에 대 한 기본 18 개 코어 및 ram 32,256 MB 대신 21 권장 되는 Vm을 지 원하는 데 필요 합니다.

14. App Service Vm 인프라를 설치 하는 데 사용할 플랫폼 이미지를 선택 하 고 클릭 **다음**:

    ![플랫폼 이미지 선택](media/app-service-deploy-ha/13.png)

15. App Service 클릭 하 여 사용할 인프라 역할 자격 증명 정보를 제공 **다음**:

    ![인프라 역할 자격 증명](media/app-service-deploy-ha/14.png)

16. App Service를 배포 하 고 클릭 하는 데 사용할 정보를 검토 **다음** 배포를 시작 합니다. 

    ![설치 요약 검토](media/app-service-deploy-ha/15.png)

17. App Service 배포 진행률을 검토 합니다. 특정 배포 구성 및 하드웨어에 따라 1 시간 이상 걸릴 수 있습니다. 설치 관리자를 성공적으로 완료 되 면 선택 **종료**합니다.

    ![설치 완료](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>다음 단계

[App Service 확장](azure-stack-app-service-add-worker-roles.md)합니다. 사용자 환경에서 예상된 응용 프로그램 요구에 맞게 추가 App Service 인프라 역할 작업자를 추가 해야 합니다. 기본적으로 Azure Stack에서 App Service는 무료 및 공유 작업자 계층을 지원합니다. 다른 작업자 계층을 추가 하려면 더 많은 작업자 역할을 추가 해야 합니다.

[배포 원본 구성](azure-stack-app-service-configure-deployment-sources.md)합니다. 추가 구성은 GitHub, BitBucket, OneDrive 및 DropBox와 같은 여러 소스 제어 공급자 로부터 주문형 배포를 지원 하기 위해 필요 합니다.
