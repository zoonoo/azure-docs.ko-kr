---
title: Azure Stack에서 오프 라인 환경에서 App Service를 배포 합니다. | Microsoft Docs
description: AD FS에서 보안이 유지 되는 연결이 끊긴된 Azure Stack 환경에서 App Service를 배포 하는 방법에 대 한 자세한 지침입니다.
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
ms.date: 01/11/2019
ms.author: anwestg
ms.openlocfilehash: db4c0f2d1197a190b33bd297bb597fd19057d875
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230342"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>AD FS에서 보안이 유지 되는 연결이 끊긴된 Azure Stack 환경에는 App Service 리소스 공급자 추가

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!IMPORTANT]
> Azure Stack 통합 시스템 1809 업데이트를 적용 하거나 Azure 앱 서비스 1.4를 배포 하기 전에 최신 Azure Stack 개발 키트를 배포 합니다.

이 문서의 지침에 따라 설치할 수 있습니다 합니다 [App Service 리소스 공급자](azure-stack-app-service-overview.md) 는 Azure Stack 환경:

- 인터넷에 연결 되어 있지
- Active Directory Federation Services (AD FS)로 보호 됩니다.

 > [!IMPORTANT]
 > 리소스 공급자를 배포 하기 전에 새로운 기능, 수정 및 배포에 영향을 줄 수 있는 알려진된 문제에 대 한 자세한 릴리스 정보를 검토 합니다.
 
App Service 리소스 공급자에 오프 라인 Azure Stack 배포를 추가 하려면 이러한 최상위 작업을 완료 해야 합니다.

1. 완료 합니다 [필수 조건 단계](azure-stack-app-service-before-you-get-started.md) (같은 인증서를 구입 하는 소요 될 수 수신 하는 데 며칠이).
2. [다운로드 및 설치 및 도우미 파일 추출](azure-stack-app-service-before-you-get-started.md) 인터넷에 연결 하는 컴퓨터에 있습니다.
3. 오프 라인 설치 패키지를 만듭니다.
4. Appservice.exe 설치 관리자 파일을 실행 합니다.

## <a name="create-an-offline-installation-package"></a>오프 라인 설치 패키지 만들기

연결이 끊어진된 환경에서 App Service에 배포 하려면 먼저 만들어야 오프 라인 설치 패키지를 인터넷에 연결 되어 있는 컴퓨터에서.

1. 인터넷에 연결 되어 있는 컴퓨터에서 AppService.exe 설치 관리자를 실행 합니다.

2. 클릭 **Advanced** > **오프 라인 설치 패키지 만들기**합니다.

    ![App Service 설치 관리자][1]

3. App Service 설치 관리자를 오프 라인 설치 패키지를 만들고 경로를 표시 합니다. 클릭할 수 **폴더 열기** 파일 탐색기에서 폴더를 열려고 합니다.

    ![App Service 설치 관리자](media/azure-stack-app-service-deploy-offline/image02.png)

4. Azure Stack 호스트 컴퓨터에 설치 관리자 (AppService.exe) 및 오프 라인 설치 패키지를 복사 합니다.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Azure Stack에서 App Service의 오프 라인 설치를 완료 합니다.

1. Azure Stack 관리자 Azure 리소스 관리 끝점에 연결할 수 있는 컴퓨터에서 관리자 권한으로 appservice.exe를 실행 합니다.

2. 클릭 **Advanced** > **오프 라인 설치를 완료**합니다.

    ![App Service 설치 관리자][2]

3. 이전에 만든 오프 라인 설치 패키지의 위치를 찾은 다음 클릭 **다음**합니다.

    ![App Service 설치 관리자](media/azure-stack-app-service-deploy-offline/image04.png)

4. 검토 및 Microsoft 소프트웨어 라이선스 조건에 동의 하 고, 클릭 **다음**합니다.

5. 검토 및 제 3 자 사용 조건에 동의 하 고, 클릭 **다음**합니다.

6. App Service 클라우드 구성 정보가 올바른지 확인 합니다. 기본 설정을 사용 하는 Azure Stack 개발 키트 배포 하는 동안 경우 여기에서 기본 값을 수락 수 있습니다. 그러나 옵션을 사용자 지정 된 Azure Stack을 배포 하거나 통합된 시스템에서 배포 하는 경우를 반영 하도록이 창에서 값을 편집 해야 합니다. 예를 들어 도메인 접미사 mycloud.com를 사용 하는 경우 Azure Stack 테 넌 트 Azure Resource Manager 끝점 관리 변경 해야 합니다. <region>. mycloud.com 합니다. 귀하의 정보를 확인 한 후 클릭 **다음**합니다.

    ![App Service 설치 관리자][3]

7. 다음 페이지에서
    1. 클릭 합니다 **Connect** 단추 옆에 **Azure Stack 구독** 상자입니다.
        - 관리자 계정을 제공 합니다. 예: cloudadmin@azurestack.local 암호를 입력 하 고 클릭 **로그인**합니다.
    2. 에 **Azure Stack 구독** 상자를 선택 합니다 **공급자 구독 기본**.
    
    > [!NOTE]
    > App Service에 배포할 수 있습니다 합니다 **기본 공급자 구독**합니다.
    >
    
    3. 에 **Azure Stack 위치** 상자에서 배포 하는 지역에 해당 하는 위치를 선택 합니다. 예를 들어 선택 **로컬** 경우 Azure Stack 개발 키트를 배포 합니다.
    4. **다음**을 클릭합니다.

    ![App Service 설치 관리자][4]

8. 이제 단계를 통해 구성 된 기존 가상 네트워크에 배포할 수 있는 옵션이 [여기](azure-stack-app-service-before-you-get-started.md#virtual-network), 또는 가상 네트워크와 연결 된 서브넷을 만들려면 App Service 설치 관리자를 허용 합니다.
    1. 선택 **기본 설정 사용 하 여 VNet 만들기**에서 기본값을 적용 하 고 클릭 **다음**, 또는;
    2. 선택 **기존 VNet 및 서브넷을 사용 하 여**입니다.
        1. 선택 된 **리소스 그룹** ; 가상 네트워크를 포함 하는
        2. 올바른 선택 **가상 네트워크** ;에 배포 하려는 이름
        3. 올바른 **서브넷** 각 필수 역할 서브넷;에 대 한 값
        4. **다음**을 누릅니다

    ![App Service 설치 관리자][5]

9. 파일 공유에 대 한 정보를 입력 한 다음 클릭 **다음**합니다. 파일 서버의 IP 주소나 정규화 된 도메인 이름, 파일 공유의 주소를 사용 해야 합니다. 예를 들어 \\\appservicefileserver.local.cloudapp.azurestack.external\websites, 또는 \\\10.0.0.1\websites

    > [!NOTE]
    > 설치 관리자를 계속 하기 전에 파일 공유에 대 한 연결을 테스트 하려고 합니다.  그러나 기존 가상 네트워크에 배포 하기로 선택한 경우 설치 관리자 파일 공유에 연결할 수 있습니다 및 계속할 것인지 묻는 경고를 표시 합니다.  파일 공유 정보를 확인 하 고 올바른 경우 계속 합니다.
    >
    >

   ![App Service 설치 관리자][8]

10. 다음 페이지에서
    1. 에 **응용 프로그램 ID** 상자에 id (Azure AD)를에서 사용 하는 응용 프로그램에 대 한 GUID를 입력 합니다.
    2. 에 **Id 응용 프로그램 인증서 파일** 상자, 입력 (또는 이동) 인증서 파일의 위치입니다.
    3. 에 **Id 응용 프로그램 인증서 암호** 상자에 인증서에 대 한 암호를 입력 합니다. 이 암호는 인증서를 만드는 스크립트를 사용 하는 경우의 참고를 변경한 것입니다.
    4. 에 **루트 인증서 파일을 Azure Resource Manager** 상자, 입력 (또는 이동) 인증서 파일의 위치입니다.
    5. **다음**을 클릭합니다.

    ![App Service 설치 관리자][10]

11. 세 개의 각에 대 한 인증서 파일 상자, 클릭 **찾아보기** 적절 한 인증서 파일로 이동 합니다. 각 인증서에 대 한 암호를 제공 해야 합니다. 이 인증서는에서 만든 것을 [필요한 인증서 단계 만들기](azure-stack-app-service-before-you-get-started.md#get-certificates)합니다. 클릭 **다음** 모든 정보를 입력 한 후입니다.

    | Box | 인증서 파일 이름 예 |
    | --- | --- |
    | **App Service 기본 SSL 인증서 파일** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL 인증서 파일** | api.appservice.local.AzureStack.external.pfx |
    | **App Service 게시자 SSL 인증서 파일** | ftp.appservice.local.AzureStack.external.pfx |

    인증서 파일 이름을 사용 하지 않는 인증서를 만들 때 다른 도메인 접미사를 사용한 경우 *로컬입니다. AzureStack.external*합니다. 대신, 사용자 지정 도메인 정보를 사용 합니다.

    ![App Service 설치 관리자][11]

12. App Service 리소스 공급자 데이터베이스를 호스트 하 고 클릭 하는 데 서버 인스턴스에 대 한 SQL Server 세부 정보를 입력 **다음**합니다. 설치 관리자는 SQL 연결 속성의 유효성을 검사 합니다. 있습니다 **해야** 내부 ip 또는 SQL Server 이름에 대 한 정규화 된 도메인 이름을 입력 합니다.

    > [!NOTE]
    > 설치 관리자를 계속 하기 전에 SQl Server에 대 한 연결을 테스트 하려고 합니다.  그러나 기존 가상 네트워크에 배포 하기로 선택한 경우 설치 관리자 SQL Server에 연결할 수 있습니다 및 계속할 것인지 묻는 경고 메시지가 표시 됩니다.  SQL Server 정보를 확인 하 고 올바른 경우 계속 합니다.
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
   
   ![App Service 설치 관리자][12]

13. 역할 인스턴스 및 SKU 옵션을 검토 합니다. 기본값은 인스턴스 및 ASDK 배포에서 각 역할에 대 한 최소 SKU의 최소 수를 사용 하 여 채워집니다. 배포를 계획 하는 데 vCPU 및 메모리 요구 사항 요약이 제공 됩니다. 에 항목을 선택한 후, 클릭 **다음**합니다.

     > [!NOTE]
     > 프로덕션 배포에 대 한 지침을 따릅니다 [용량 Azure Stack에서 Azure App Service 서버 역할에 대 한 계획](azure-stack-app-service-capacity-planning.md)합니다.
     >
     >

    | 역할 | 최소 인스턴스 | 최소 SKU | 메모 |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2-(2 vCPU, 3584MB) | 관리 하 고 App Service 클라우드 상태를 유지 합니다. |
    | 관리 | 1 | Standard_A2-(2 개 Vcpu, 3584MB) | App Service Azure Resource Manager 및 API 끝점, 포털 확장 (관리자, 테 넌 트, Functions 포털) 및 데이터 서비스를 관리합니다. 장애 조치를 지원 하기 위해 권장 되는 인스턴스 2로 증가 합니다. |
    | 게시자 | 1 | Standard_A1-(1 개 vCPU, 1,792MB) | FTP 및 웹 배포를 통해 콘텐츠를 게시합니다. |
    | FrontEnd | 1 | Standard_A1-(1 개 vCPU, 1,792MB) | App Service 응용 프로그램에 요청을 라우팅합니다. |
    | 공유 작업자 | 1 | Standard_A1-(1 개 vCPU, 1,792MB) | 호스트 웹 또는 API 응용 프로그램 및 Azure Functions 앱. 더 많은 인스턴스를 추가할 수 있습니다. 운영자로 귀하의 제품을 정의 하 고 모든 SKU 계층을 선택할 수 있습니다. 계층에는 하나의 vCPU 개가 있어야 합니다. |

    ![App Service 설치 관리자][14]

    > [!NOTE]
    > **Windows Server 2016 Core 지원 되는 플랫폼 이미지를 사용 하 여 Azure App Service on Azure Stack을 사용 하 여 아닙니다.  프로덕션 배포에 대 한 평가판 이미지를 사용 하지 마세요.  Azure Stack의 azure App Service 배포에 사용 되는 이미지에 microsoft.net 3.5.1 SP1 활성화 되는 필요 합니다.   Marketplace 이미지에는이 기능을 사용할 수 없는 Windows Server 2016 신디케이티드를 만들고이 미리 활성화를 사용 하 여 Windows Server 2016 이미지를 사용 해야 하므로 합니다.**

14. 에 **플랫폼 이미지 선택** 에서 App Service 클라우드를 위한 계산 리소스 공급자에서 사용할 수 있는 배포 Windows Server 2016 가상 머신 이미지를 선택 합니다. **다음**을 클릭합니다.

15. 다음 페이지에서
     1. 작업자 역할 가상 머신 관리자 사용자 이름 및 암호를 입력 합니다.
     2. 기타 역할 가상 머신 관리자 사용자 이름 및 암호를 입력 합니다.
     3. **다음**을 클릭합니다.

    ![App Service 설치 관리자][16]

16. 요약 페이지에서
    1. 선택 사항을 확인 합니다. 변경 하려면 사용 합니다 **이전** 이전 페이지를 방문 하는 단추입니다.
    2. 구성이 올바른 경우 확인란을 선택 합니다.
    3. 배포를 시작 하려면 **다음**합니다.

    ![App Service 설치 관리자][17]

17. 다음 페이지에서
    1. 설치 진행률을 추적 합니다. Azure Stack에서 app Service는 기본 선택 사항을 기반 배포를 약 60 분.
    2. 설치 관리자를 성공적으로 완료 되 면 클릭 **종료**합니다.

    ![App Service 설치 관리자][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Azure Stack 설치에서 App Service의 유효성을 검사합니다

1. Azure Stack 관리 포털에서로 이동 **App Service 관리-** 합니다.

2. 상태에서 개요에 있는지 확인 합니다 **상태** 표시 **모든 역할이 준비 됨**합니다.

    ![App Service 관리](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> 기존 가상 네트워크에 파일 서버에 연결 하는 내부 IP 주소를 배포 하려는 경우 아웃 바운드 보안 규칙을 추가 하 있습니다 작업자 서브넷과 파일 서버 간에 SMB 트래픽을 사용 하도록 설정 해야 합니다.  이렇게 하려면 관리 포털에서 WorkersNsg 이동한 다음 속성을 사용 하 여 아웃 바운드 보안 규칙을 추가 합니다.
> * 원본: 모두
> * 원본 포트 범위: *
> * 대상: IP 주소
> * 대상 IP 주소 범위: Ip 범위에 파일 서버에 대 한
> * 대상 포트 범위: 445
> * 프로토콜: TCP
> * 작업: 허용
> * 우선 순위: 700
> * 이름: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Azure Stack에서 App Service 테스트 드라이브

배포 하 고 App Service 리소스 공급자 등록 후에 사용자 웹 앱 및 API 앱을 배포할 수 있는지 테스트 합니다.

> [!NOTE]
> 계획 내 Microsoft.Web 네임 스페이스에 있는 제품을 만드는 데 필요 합니다. 그런 다음이 제품을 구독 하는 테 넌 트 구독을 보유 해야 합니다. 자세한 내용은 [만들기 제품](azure-stack-create-offer.md) 하 고 [계획 만들기](azure-stack-create-plan.md)합니다.
>
> 있습니다 *해야* Azure Stack에서 App Service를 사용 하는 응용 프로그램을 만들려면 테 넌 트 구독을가지고 있습니다. 서비스 관리 관리자 포털 내에서 완료할 수 있는 유일한 기능은 App Service 리소스 공급자 관리 관련 되어 있습니다. 용량을 추가, 배포 원본 구성 및 작업자 계층 및 Sku를 추가 합니다. 이러한 기능에 포함 됩니다.
>
> 세 번째 technical preview를 기준으로 만들 웹, API 및 Azure 함수 앱, 테 넌 트 포털을 사용 하 고 테 넌 트 구독을 보유 해야 합니다.

1. Azure Stack 테 넌 트 포털에서 클릭 **+ 리소스 만들기** > **웹 + 모바일** > **Webapp**합니다.

2. 에 **웹 앱** 블레이드에서에 이름을 입력 합니다 **웹 앱** 상자입니다.

3. 아래 **리소스 그룹**, 클릭 **새로 만들기**합니다. 에 이름을 입력 합니다 **리소스 그룹** 상자입니다.

4. **App Service 계획/위치** > **새로 만들기**를 클릭합니다.

5. 에 **App Service 계획** 블레이드에서에 이름을 입력 합니다 **App Service 계획** 상자입니다.

6. 클릭 **가격 책정 계층** > **무료 공유** 하거나 **공유 공유** > **선택**  >   **확인** > **만듭니다**합니다.

7. 1 분에서의 대시보드에서 새 웹 앱에 대 한 타일이 표시 됩니다. 타일을 클릭 합니다.

8. 에 **웹 앱** 블레이드에서 클릭 **찾아보기** 이 앱에 대 한 기본 웹 사이트를 볼 수 있습니다.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>(선택 사항) DNN, WordPress, Django 웹 사이트 배포

1. Azure Stack 테 넌 트 포털에서 클릭 **+**, Azure Marketplace로 이동, Django 웹 사이트를 배포 및 성공적으로 완료 될 때까지 기다립니다. Django 웹 플랫폼에는 파일 시스템 기반 데이터베이스를 사용합니다. 이 SQL 또는 MySQL 등의 모든 추가 리소스 공급자에 필요 하지 않습니다.

2. 또한 MySQL 리소스 공급자를 배포한 경우 Marketplace에서 WordPress 웹 사이트를 배포할 수 있습니다. 데이터베이스 매개 변수를 묻는 경우 사용자 이름으로 입력 *User1@Server1*, 사용자 이름 및 선택한 서버 이름을 사용 하 여 합니다.

3. 또한 SQL Server 리소스 공급자를 배포한 경우 Marketplace에서 DNN 웹 사이트를 배포할 수 있습니다. 데이터베이스 매개 변수를 묻는 경우 리소스 공급자에 연결 된 SQL Server를 실행 하는 컴퓨터에서 데이터베이스를 선택 합니다.

## <a name="next-steps"></a>다음 단계

또한에서 사용할 수 있는 다른 [서비스 (PaaS) 플랫폼](azure-stack-tools-paas-services.md)합니다.

- [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
