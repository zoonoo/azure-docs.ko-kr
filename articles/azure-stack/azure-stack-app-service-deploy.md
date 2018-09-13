---
title: 'App Services를 배포 합니다: Azure Stack | Microsoft Docs'
description: Azure Stack에서 App Service를 배포 하기 위한 자세한 지침
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: anwestg
ms.openlocfilehash: ae742bbe1e5b751a8a8cabf378afd049728c3b8a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717508"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Azure Stack에 App Service 리소스 공급자 추가

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에서 App Service를 배포 하려면이 문서의 지침을 사용 합니다.

>[!IMPORTANT]  
>Azure Stack 통합 시스템 1807 업데이트를 적용 하거나 Azure 앱 서비스 1.3을 배포 하기 전에 최신 Azure Stack 개발 키트 ASDK ()를 배포 합니다.

웹 및 API 응용 프로그램을 만드는 기능에 사용자를 제공할 수 있습니다. 를 사용자가 이러한 응용 프로그램을 만들 수 있도록 해야 합니다.

 - 추가 된 [App Service 리소스 공급자](azure-stack-app-service-overview.md) 이 문서에 설명 된 단계를 사용 하 여 Azure Stack 배포 합니다.
 - App Service 리소스 공급자를 설치한 후에 제품 및 계획에 포함할 수 있습니다. 사용자 서비스 및 응용 프로그램을 만들기 시작 하려면 다음 구독할 수 있습니다.

> [!IMPORTANT]  
> 리소스 공급자 설치 관리자를 실행 하기 전에의 지침을 따랐는지 확인 하십시오 [시작 하기 전에](azure-stack-app-service-before-you-get-started.md)입니다.

## <a name="run-the-app-service-resource-provider-installer"></a>App Service 리소스 공급자 설치 관리자를 실행 합니다.

1 시간 이상는 App Service 리소스 공급자를 설치 합니다. 얼마나 많은 역할 인스턴스 수에 따라 달라 집니다 필요한 시간의 길이 배포 합니다. 배포 하는 동안 설치 관리자는 다음 작업을 실행 합니다.

 - 지정된 된 Azure Stack 저장소 계정에서 blob 컨테이너를 만듭니다.
 - App Service에 대 한 DNS 영역 및 항목을 만듭니다.
 - App Service 리소스 공급자를 등록 합니다.
 - App Service 갤러리 항목을 등록 합니다.

App Service 리소스 공급자를 배포 하려면 다음이 단계를 수행 합니다.

1. Azure Stack 관리자 Azure 리소스 관리 끝점에 액세스할 수 있는 컴퓨터에서 관리자 권한으로 appservice.exe를 실행 합니다.

2. 선택 **App Service 배포 또는 최신 버전으로 업그레이드**합니다.

    ![App Service 설치 관리자][1]

3. 검토 하 고 Microsoft 소프트웨어 사용 조건 동의 선택한 **다음**합니다.

4. 검토-타사 라이선스 조건에 동의 하 고 선택한 **다음**합니다.

5. App Service 클라우드 구성 정보가 올바른지 확인 합니다. 기본 설정을 사용 하는 Azure Stack 개발 키트 (ASDK) 배포 하는 동안 경우에 기본 값을 사용할 수 있습니다. 그러나 ASDK, 배포 또는 Azure Stack 통합 시스템에서 배포 하는 경우 옵션 사용자 지정 하는 경우에 차이점이 반영 되도록이 창의 값을 편집 해야 합니다.

   예를 들어 도메인 접미사 mycloud.com를 사용 하는 경우 Azure Stack 테 넌 트 Azure Resource Manager 끝점 관리 변경 해야 합니다. &lt;지역&gt;. mycloud.com 합니다. 이러한 설정을 검토 하 고 선택한 **다음** 설정을 저장 합니다.

   ![App Service 설치 관리자][2]

6. 다음 앱 서비스 설치 관리자 페이지에서 다음이 단계를 수행 합니다.

    a. 선택 **Connect** 옆에 **Azure Stack 구독**합니다.

     - Azure Active Directory (Azure AD)를 사용 하는 경우 Azure AD 관리자 계정 및 Azure Stack을 배포할 때 제공한 암호를 입력 합니다. 선택 **로그인**합니다.
     - Active Directory Federation Services (AD FS)를 사용 하는 경우에 관리자 계정을 제공 합니다. 예: cloudadmin@azurestack.local 암호를 입력 한 다음 선택 **로그인**합니다.

   b. **Azure Stack 구독**를 선택 합니다 **공급자 구독 기본**입니다.

     >[!NOTE]
     >현재 App Service만에 배포할 수는 **기본 공급자 구독**합니다.

   다. 에 **Azure Stack 위치**를 배포 하는 지역에 해당 하는 위치를 선택 합니다. 예를 들어 선택 **로컬** 경우 Azure Stack 개발 키트를 배포 합니다.

    ![App Service 설치 관리자][3]

7. 이제 구성 된 기존 가상 네트워크에 배포할 수 있습니다 [이 단계를 사용 하 여](azure-stack-app-service-before-you-get-started.md#virtual-network), 또는 App Service 설치 프로그램에서 새 가상 네트워크 및 서브넷을 만듭니다. VNet을 만들려면 다음이 단계를 수행 합니다.

   a. 선택 **기본 설정 사용 하 여 VNet 만들기**기본값을 적용 하 고 선택한 **다음**합니다.

   b. 또는 선택할 **기존 VNet 및 서브넷을 사용 하 여**입니다. 다음 작업을 완료 합니다.

     - 선택 된 **리소스 그룹** 가상 네트워크를 포함 하는 합니다.
     - 선택 된 **가상 네트워크** 에 배포 하려는 이름입니다.
     - 올바른 **서브넷** 각 필수 역할 서브넷에 대 한 값입니다.
     - **다음**을 선택합니다.

   ![App Service 설치 관리자][4]

8. 파일 공유에 대 한 정보를 입력 하 고 선택한 **다음**합니다. 파일 공유의 주소는 파일 서버의 IP 주소나를 완전히 정규화 된 도메인 이름 (FQDN)을 사용 해야 합니다. 예를 들어 \\\appservicefileserver.local.cloudapp.azurestack.external\websites, 또는 \\\10.0.0.1\websites 합니다.

   >[!NOTE]
   >관리자는 계속 진행 하기 전에 파일 공유에 대 한 연결을 테스트 하려고 합니다. 그러나 기존 가상 네트워크에 배포 하는이 연결 테스트가 실패할 수 있습니다. 계속 하려면 경고 및 프롬프트를 지정 하는 합니다. Fileshare 정보가 올바른 경우 배포를 계속 합니다.

   ![App Service 설치 관리자][7]

9. 다음 앱 서비스 설치 관리자 페이지에서 다음이 단계를 수행 합니다.

   a. 에 **응용 프로그램 ID** 상자에 id (Azure AD)를에서 사용 하는 응용 프로그램에 대 한 GUID를 입력 합니다.

   b. 에 **Id 응용 프로그램 인증서 파일** 상자, 입력 (또는 이동) 인증서 파일의 위치입니다.

   다. 에 **Id 응용 프로그램 인증서 암호** 상자에 인증서에 대 한 암호를 입력 합니다. 이 암호는 인증서를 만드는 스크립트를 사용 하는 경우의 참고를 변경한 것입니다.

   d. 에 **루트 인증서 파일을 Azure Resource Manager** 상자, 입력 (또는 이동) 인증서 파일의 위치입니다.

   e. **다음**을 선택합니다.

   ![App Service 설치 관리자][9]

10. 세 개의 인증서 파일 상자에서 각각에 대 한 선택 **찾아보기** 적절 한 인증서 파일로 이동 합니다. 각 인증서에 대 한 암호를 제공 해야 합니다. 이 인증서는에서 만든 것을 [필요한 인증서 단계 만들기](azure-stack-app-service-before-you-get-started.md#get-certificates)합니다. 선택 **다음** 모든 정보를 입력 한 후입니다.

    | Box | 인증서 파일 이름 예 |
    | --- | --- |
    | **App Service 기본 SSL 인증서 파일** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL 인증서 파일** | api.appservice.local.AzureStack.external.pfx |
    | **App Service 게시자 SSL 인증서 파일** | ftp.appservice.local.AzureStack.external.pfx |

    인증서 파일 이름을 사용 하지 않는 인증서를 만들 때 다른 도메인 접미사를 사용한 경우 *로컬입니다. AzureStack.external*합니다. 대신, 사용자 지정 도메인 정보를 사용 합니다.

    ![App Service 설치 관리자][10]

11. App Service 리소스 공급자 데이터베이스를 호스트 하 고 다음을 선택 하는 데 사용 하는 서버 인스턴스에 대 한 SQL Server 세부 정보를 입력 **다음**합니다. 설치 관리자는 SQL 연결 속성의 유효성을 검사 합니다.

    > [!NOTE]
    > 관리자는 계속 진행 하기 전에 SQL Server에 대 한 연결을 테스트 하려고 합니다. 그러나 기존 가상 네트워크에 배포 하는 경우이 연결 테스트 파일 수 있습니다. 계속 하려면 경고 및 프롬프트를 지정 하는 합니다. SQL Server 정보가 올바른 경우 배포를 계속 합니다.
    >
    > Azure Stack 1.3 이상에서 Azure App Service에서 설치 관리자는 SQL Server SQL Server 수준에서 사용 하도록 설정 하는 데이터베이스 포함에 있는지 확인 합니다.  없는 경우 예외가 발생 하 여 묻는 메시지가 나타납니다.
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > 참조 된 [Azure Stack 1.3에서 Azure App Service에 대 한 릴리스 정보](azure-stack-app-service-release-notes-update-three.md) 대 한 자세한 내용은 합니다.

    ![App Service 설치 관리자][11]

12. 역할 인스턴스 및 SKU 옵션을 검토 합니다. 기본값을 최소 인스턴스 수 및 ASDK 배포에서 각 역할에 대 한 최소 SKU를 사용 하 여 채웁니다. 배포를 계획 하는 데 vCPU 및 메모리 요구 사항 요약이 제공 됩니다. 선택 항목을 변경한 후 선택 **다음**합니다.

    >[!NOTE]
    >지침에 따라 프로덕션 배포용 [용량 Azure Stack에서 Azure App Service 서버 역할에 대 한 계획](azure-stack-app-service-capacity-planning.md)합니다.

    | 역할 | 최소 인스턴스 | 최소 SKU | 메모 |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2-(2 vCPU, 3584MB) | 관리 하 고 App Service 클라우드 상태를 유지 합니다. |
    | 관리 | 1 | Standard_A2-(2 개 Vcpu, 3584MB) | App Service Azure Resource Manager 및 API 끝점, 포털 확장 (관리자, 테 넌 트, Functions 포털) 및 데이터 서비스를 관리합니다. 장애 조치를 지원 하기 위해 권장 되는 인스턴스 2로 증가 합니다. |
    | 게시자 | 1 | Standard_A1-(1 개 vCPU, 1,792MB) | FTP 및 웹 배포를 통해 콘텐츠를 게시합니다. |
    | FrontEnd | 1 | Standard_A1-(1 개 vCPU, 1,792MB) | App Service 응용 프로그램에 요청을 라우팅합니다. |
    | 공유 작업자 | 1 | Standard_A1-(1 개 vCPU, 1,792MB) | 호스트 웹 또는 API 응용 프로그램 및 Azure Functions 앱. 더 많은 인스턴스를 추가할 수 있습니다. 운영자로 귀하의 제품을 정의 하 고 모든 SKU 계층을 선택할 수 있습니다. 계층에는 하나의 vCPU 개가 있어야 합니다. |

    ![App Service 설치 관리자][13]

    >[!NOTE]
    >**Windows Server 2016 Core에 지원 되는 플랫폼 이미지를 사용 하 여 Azure Stack에서 Azure App Service를 사용 하지 않습니다.  프로덕션 배포에 대 한 평가판 이미지를 사용 하지 마세요.**

13. 에 **플랫폼 이미지 선택** App Service 클라우드를 위한 계산 리소스 공급자에서 사용 가능한 이미지에서 배포 Windows Server 2016 가상 머신 이미지를 선택 합니다. **다음**을 선택합니다.

14. 다음 앱 서비스 설치 관리자 페이지에서 다음이 단계를 수행 합니다.

     a. 작업자 역할 가상 머신 관리자 사용자 이름 및 암호를 입력 합니다.

     b. 기타 역할 가상 머신 관리자 사용자 이름 및 암호를 입력 합니다.

     다. **다음**을 선택합니다.

    ![App Service 설치 관리자][15]

15. 앱 서비스 설치 관리자 요약 페이지에서 다음이 단계를 수행 합니다.

    a. 선택 사항을 확인 합니다. 변경 하려면 사용 합니다 **이전** 이전 페이지를 방문 하는 단추입니다.

    b. 구성이 올바른 경우 확인란을 선택 합니다.

    다. 배포를 시작 하려면 **다음**합니다.

    ![App Service 설치 관리자][16]

16. 다음 앱 서비스 설치 관리자 페이지에서 다음이 단계를 수행 합니다.

    a. 설치 진행률을 추적 합니다. Azure Stack에서 app Service는 기본 선택 사항을 기반 배포를 약 60 분.

    b. 설치 관리자를 성공적으로 완료 되 면 선택 **종료**합니다.

    ![App Service 설치 관리자][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Azure Stack 설치에서 App Service의 유효성을 검사합니다

1. Azure Stack 관리 포털에서로 이동 **App Service 관리-** 합니다.

2. 상태에서 개요에 있는지 확인 합니다 **상태** 표시 **모든 역할이 준비 됨**합니다.

    ![App Service 관리](media/azure-stack-app-service-deploy/image12.png)

   >[!IMPORTANT]
   >기존 가상 네트워크에 배포 하 고 내부 IP 주소를 사용 하 여 파일 서버에 연결할, 아웃 바운드 보안 규칙을 추가 해야 합니다. 이 규칙에 작업자 서브넷과 파일 서버 간에 SMB 트래픽을 사용 하도록 설정 합니다.  이렇게 하려면 관리 포털에서 WorkersNsg 이동한 다음 속성을 사용 하 여 아웃 바운드 보안 규칙을 추가 합니다.<br>
    >  - 원본: 모든
    >  - 원본 포트 범위: *
    >  - 대상: IP 주소
    >  - 대상 IP 주소 범위: Ip 범위에 파일 서버에 대 한
    >  - 대상 포트 범위: 445
    >  - 프로토콜: TCP
    >  - 작업: 허용
    >  - 우선 순위: 700
    >  - 이름: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Azure Stack에서 App Service 테스트 드라이브

배포 하 고 App Service 리소스 공급자 등록 후에 사용자 웹 앱 및 API 앱을 배포할 수 있는지 테스트 합니다.

>[!NOTE]
>계획 Microsoft.Web 네임 스페이스에 있는 제품을 만드는 데 필요 합니다. 제품을 구독 하는 테 넌 트 구독을 해야 합니다. 자세한 내용은 [만들기 제품](azure-stack-create-offer.md) 하 고 [계획 만들기](azure-stack-create-plan.md)합니다.
>
>있습니다 *해야* Azure Stack에서 App Service를 사용 하는 응용 프로그램을 만들려면 테 넌 트 구독을가지고 있습니다. App Service 리소스 공급자 관리 서비스 관리 관리자 포털에서 완료할 수 있는 작업만 관련 됩니다. 여기에 용량을 추가, 배포 원본 구성 및 작업자 계층 및 Sku를 추가 합니다.
>
>만드는 웹, API 및 Azure 함수 앱, 테 넌 트 포털을 사용 하 고 테 넌 트 구독을 보유 해야 합니다.
>

테스트 웹 앱을 만들려면 다음이 단계를 수행 합니다.

1. Azure Stack 사용자 포털에서 선택 **+ 리소스 만들기** > **웹 + 모바일** > **Webapp**합니다.

2. 아래 **웹 앱**에 이름을 입력 **웹 앱**합니다.

3. 아래 **리소스 그룹**를 선택 **새로 만들기**합니다. 에 대 한 이름을 입력 합니다 **리소스 그룹**합니다.

4. 선택 **App Service 계획/위치** > **새로 만들기**합니다.

5. 아래 **App Service 계획**에 대 한 이름을 입력 합니다 **App Service 계획**합니다.

6. 선택 **가격 책정 계층** > **무료 공유** 하거나 **공유 공유** > **선택**  >  **확인** > **만들**합니다.

7. 새 웹 앱에 대 한 타일을 대시보드에 표시 됩니다. 타일을 선택 합니다.

8. **웹 앱**를 선택 **찾아보기** 이 앱에 대 한 기본 웹 사이트를 볼 수 있습니다.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>(선택 사항) DNN, WordPress, Django 웹 사이트 배포

1. Azure Stack 테 넌 트 포털에서 선택 **+**, Azure Marketplace로 이동, Django 웹 사이트를 배포 하 고 배포가 끝나기를 기다립니다. Django 웹 플랫폼에는 파일 시스템 기반 데이터베이스를 사용합니다. 이 SQL 또는 MySQL 등의 모든 추가 리소스 공급자에 필요 하지 않습니다.

2. 또한 MySQL 리소스 공급자를 배포한 경우 Marketplace에서 WordPress 웹 사이트를 배포할 수 있습니다. 데이터베이스 매개 변수를 묻는 경우 사용자 이름으로 입력 *User1@Server1*, 사용자 이름 및 선택한 서버 이름을 사용 하 여 합니다.

3. 또한 SQL Server 리소스 공급자를 배포한 경우 Marketplace에서 DNN 웹 사이트를 배포할 수 있습니다. 데이터베이스 매개 변수를 묻는 경우 리소스 공급자에 연결 된 SQL Server를 실행 하는 컴퓨터에서 데이터베이스를 선택 합니다.

## <a name="next-steps"></a>다음 단계

또한에서 사용할 수 있는 다른 [서비스 (PaaS) 플랫폼](azure-stack-tools-paas-services.md)합니다.

 - [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
 - [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
