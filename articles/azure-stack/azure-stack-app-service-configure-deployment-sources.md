---
title: Azure Stack에서 App Services에 대 한 배포 원본 구성 | Microsoft Docs
description: 서비스 관리자가 Azure Stack에서 App Services에 대 한 배포 원본 (Git, GitHub, BitBucket, DropBox 및 OneDrive)을 구성할 수는 방법
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 66688ae762aa646fc72045836c4ec4671bf4b7b4
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888514"
---
# <a name="configure-deployment-sources"></a>배포 원본 구성

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에서 app Service는 여러 소스 제어 공급자 로부터 주문형 배포를 지원합니다. 이 기능에는 응용 프로그램 개발자를 소스 제어 리포지토리에서 직접 배포할 수 있습니다. 사용자를 해당 리포지토리에 연결 하도록 App Service를 구성 하려면 클라우드 운영자는 Azure Stack에서 App Service와 원본 제어 공급자 간의 통합을 먼저 구성 해야 합니다.  

로컬 Git 외에도 다음과 같은 원본 제어 공급자 지원 됩니다.

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>App Service 관리에서 배포 원본 보기

1. Azure Stack 관리 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자로 합니다.
2. 이동할 **모든 서비스** 선택 합니다 **App Service**합니다.
    ![App Service 리소스 공급자 관리][1]
3. **소스 제어 구성**을 클릭합니다. 모든 구성 된 배포 원본 목록이 볼 수 있습니다.
    ![App Service 리소스 공급자 관리 소스 제어 구성][2]

## <a name="configure-github"></a>GitHub 구성

이 작업을 완료 하려면 GitHub 계정이 있어야 합니다. 개인 계정이 아닌 조직 계정을 사용 수 있습니다.

1. GitHub에 로그인, 이동할 https://www.github.com/settings/developers를 클릭 하 고 **새 응용 프로그램을 등록**합니다.
    ![GitHub-새 응용 프로그램 등록][3]
2. 입력을 **응용 프로그램 이름**; 예를 들어 **App Service on Azure Stack**합니다.
3. **홈 페이지 URL**을 입력합니다. 홈 페이지 URL에는 Azure Stack portal 주소 여야 합니다. 예: https://portal.local.azurestack.external
4. 입력을 **응용 프로그램 설명**합니다.
5. **권한 부여 콜백 URL**을 입력합니다. 기본 Azure Stack 배포의 경우 URL은 형태로 https://portal.local.azurestack.external/TokenAuthorize합니다. 다른 도메인에서 실행 하는 경우에 local.azurestack.external에 대 한 도메인 이름으로를 바꿉니다.
6. **응용 프로그램 등록**을 클릭합니다. 나열 된 페이지가 표시 됩니다는 **클라이언트 ID** 하 고 **클라이언트 비밀** 응용 프로그램에 대 한 합니다.
    ![GitHub - 완료된 응용 프로그램 등록][5]
7.  새 브라우저 탭 또는 창에서 Azure Stack 관리 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자로 합니다.
8.  이동할 **리소스 공급자**를 선택 합니다 **App Service 리소스 공급자 관리**합니다.
9. **소스 제어 구성**을 클릭합니다.
10. 복사 및 붙여넣기 합니다 **클라이언트 ID** 하 고 **클라이언트 암호** GitHub에 대 한 상자에 해당 입력 합니다.
11. **저장**을 클릭합니다.

## <a name="configure-bitbucket"></a>BitBucket 구성

이 작업을 완료 하려면 BitBucket 계정이 있어야 합니다. 개인 계정이 아닌 조직 계정을 사용 수 있습니다.

1. BitBucket에 로그인 하 고 이동할 **통합** 계정.
    ![BitBucket 대시보드-통합][7]
2. 클릭 **OAuth** 대 한 액세스 관리 및 **소비자 추가**합니다.
    ![BitBucket은 OAuth 소비자 추가][8]
3. 입력을 **이름을** 소비자;에 대 한 예를 들어 **App Service on Azure Stack**합니다.
4. 입력 한 **설명을** 응용 프로그램에 대 한 합니다.
5. **콜백 URL**을 입력합니다. 기본 Azure Stack 배포의 경우 콜백 URL은 형태로 https://portal.local.azurestack.external/TokenAuthorize합니다. 다른 도메인에서 실행 하는 경우 azurestack.local에 대 한 도메인 이름으로를 바꿉니다. 성공 하려면 BitBucket 통합에 대 한 URL을 여기에 나열 된 대/소문자를 수행 해야 합니다.
6. 입력 된 **URL**합니다. 이 URL은 Azure Stack 포털 이어야 합니다. URL 예를 들어 https://portal.local.azurestack.external합니다.
7. 선택 된 **권한을** 필요:
    - **리포지토리**: *읽기*
    - **웹 후크**: *읽기 및 쓰기*
8. **저장**을 클릭합니다. 이제와 함께이 새 응용 프로그램을 표시 합니다 **키** 및 **비밀**아래에 있는 **OAuth 소비자**.
    ![BitBucket 응용 프로그램 목록][9]
9.  새 브라우저 탭 또는 창에서 Azure Stack 관리 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자로 합니다.
10.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
11. **소스 제어 구성**을 클릭합니다.
12. 복사 및 붙여넣기를 **키** 에 **클라이언트 ID** 입력된 상자 및 **비밀** 에 **클라이언트 암호** BitBucket에 대 한 입력란입니다.
13. **저장**을 클릭합니다.

## <a name="configure-onedrive"></a>OneDrive 구성

이 작업을 완료 하려면 OneDrive 계정에 연결 된 Microsoft 계정이 있어야 합니다.  개인 계정이 아닌 조직 계정을 사용 수 있습니다.

> [!NOTE]
> 비즈니스용 OneDrive 계정은 현재 지원 되지 않습니다.

1. 이동할 https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm Microsoft 계정을 사용 하 여 로그인 합니다.
2. 아래 **내 응용 프로그램**, 클릭 **앱 추가**합니다.
![OneDrive 응용 프로그램][10]
3. 입력을 **이름을** 새 응용 프로그램 등록에 대 한: 입력 **App Service on Azure Stack**를 클릭 하 고 **응용 프로그램 만들기**
4. 다음 화면에서 새 응용 프로그램의 속성이 나열됩니다. 저장 된 **응용 프로그램 ID** 임시 위치로 합니다.
![OneDrive 응용 프로그램 속성][11]
5. **응용 프로그램 비밀** 아래에서 **새 암호 생성**을 클릭합니다. 기록해는 **새 암호 생성**합니다. 응용 프로그램 암호 이며를 클릭 한 후 가져올 수 없습니다 **확인**합니다.
6. 아래 **플랫폼**, 클릭 **플랫폼 추가**를 선택한 후 **웹**합니다.
7. **리디렉션 URI**를 입력합니다. 기본 Azure Stack 배포의 경우 리디렉션 URI의 형태로 https://portal.local.azurestack.external/TokenAuthorize합니다. 다른 도메인에서 실행 하는 경우 azurestack.local에 대 한 도메인 이름으로를 바꿉니다.
![OneDrive 응용 프로그램-웹 플랫폼 추가][12]
8. 추가 된 **Microsoft Graph 권한** - **위임 된 권한**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive 응용 프로그램 - Graph 권한][13]
9. **저장**을 클릭합니다.
10.  새 브라우저 탭 또는 창에서 Azure Stack 관리 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자로 합니다.
11.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
12. **소스 제어 구성**을 클릭합니다.
13. 복사 및 붙여넣기 합니다 **응용 프로그램 ID** 에 **클라이언트 ID** 입력란 및 **암호** 에 **클라이언트 암호** OneDrive에 대 한 입력란.
14. **저장**을 클릭합니다.

## <a name="configure-dropbox"></a>DropBox 구성

> [!NOTE]
> 이 작업을 완료 하려면 DropBox 계정이 있어야 합니다. 개인 계정이 아닌 조직 계정을 사용 수 있습니다.

1. 이동할 https://www.dropbox.com/developers/apps DropBox 계정 자격 증명을 사용 하 여 로그인 합니다.
2. **앱 만들기**를 클릭합니다.

    ![Dropbox 응용 프로그램][14]

3. 선택 **DropBox API**합니다.
4. 액세스 수준을 설정 **앱 폴더**합니다.
5. 응용 프로그램의 **이름**을 입력합니다.
![Dropbox 응용 프로그램 등록][15]
6. **앱 만들기**를 클릭합니다. 앱에 대 한 설정을 나열 된 페이지가 표시 됩니다 포함 **앱 키** 하 고 **앱 비밀**합니다.
7. 있는지 확인 합니다 **앱 폴더 이름** 로 설정 된 **App Service on Azure Stack**합니다.
8. 설정 된 **OAuth 2 리디렉션 URI** 을 클릭 한 다음 **추가**합니다. 기본 Azure Stack 배포의 경우 리디렉션 URI의 형태로 https://portal.local.azurestack.external/TokenAuthorize합니다. 다른 도메인에서 실행 하는 경우 azurestack.local에 대 한 도메인으로 대체 합니다.
![Dropbox 응용 프로그램 구성][16]
9.  새 브라우저 탭 또는 창에서 Azure Stack 관리 포털에 로그인 (https://adminportal.local.azurestack.external) 서비스 관리자로 합니다.
10.  **리소스 공급자**로 이동하여 **App Service 리소스 공급자 관리**를 선택합니다.
11. **소스 제어 구성**을 클릭합니다.
12. 복사 및 붙여넣기 합니다 **응용 프로그램 키** 에 **클라이언트 ID** 입력된 상자 및 **앱 암호** 에 **클라이언트 암호** DropBox에 대 한 입력란.
13. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

등에 대 한 사용자가 배포 소스를 이제 사용할 수 있습니다 [연속 배포](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment)를 [로컬 Git 배포](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), 및 [클라우드 폴더 동기화](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync)합니다.

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
