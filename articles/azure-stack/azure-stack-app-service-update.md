---
title: Azure App Service on Azure Stack 업데이트 | Microsoft Docs
description: Azure App Service on Azure Stack을 업데이트 하는 것에 대 한 자세한 지침
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: e8a75afe2c7dbe91c7c98d0d35c319088f40748f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612681"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Azure Stack의 Azure App Service를 업데이트 합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!IMPORTANT]  
> Azure Stack 통합 시스템 1809 업데이트를 적용 하거나 Azure 앱 서비스 1.4를 배포 하기 전에 최신 Azure Stack 개발 키트를 배포 합니다.
>
>

이 문서의 지침에 따라 업그레이드할 수 있습니다 합니다 [App Service 리소스 공급자](azure-stack-app-service-overview.md) 인터넷에 연결 된 Azure Stack 환경에 배포 합니다.

> [!IMPORTANT]  
> 업그레이드를 실행 하기 전에 이미 완료 되었는지 확인 합니다 [Azure Stack 리소스 공급자에서 Azure App Service 배포](azure-stack-app-service-deploy.md)

## <a name="run-the-app-service-resource-provider-installer"></a>App Service 리소스 공급자 설치 관리자를 실행 합니다.

이 과정에서 업그레이드가 됩니다.

* App Service의 이전 배포를 검색 합니다.
* 모든 업데이트 패키지 및 배포할 모든 OSS 라이브러리의 새 버전을 준비
* 저장소에 업로드
* App Service는 모든 역할 업그레이드 (컨트롤러, 관리, 프런트엔드, 게시자 및 작업자 역할)
* App Service 확장 집합 정의 업데이트
* App Service 리소스 공급자 매니페스트 업데이트

> [!IMPORTANT]
> App Service 설치 관리자는 Azure Stack 관리자 Azure Resource Manager 끝점에 연결할 수 있는 컴퓨터에서 실행 되어야 합니다.
>
>

Azure Stack에서 App Service의 배포를 업그레이드 하려면 다음이 단계를 수행 합니다.

1. 다운로드는 [App Service 설치 관리자](https://aka.ms/appsvcupdate4installer)

2. Appservice.exe 관리자 권한으로 실행

    ![App Service 설치 관리자][1]

3. 클릭 **App Service 배포 또는 최신 버전으로 업그레이드 합니다.**

4. 검토 및 Microsoft 소프트웨어 라이선스 조건에 동의 하 고, 클릭 **다음**합니다.

5. 검토 및 제 3 자 사용 조건에 동의 하 고, 클릭 **다음**합니다.

6. 확인 된 Azure Stack Azure Resource Manager 끝점 및 Active Directory 테 넌 트 정보가 올바른지 합니다. 기본 설정을 사용 하는 Azure Stack 개발 키트 배포 하는 동안 경우 여기에서 기본 값을 수락 수 있습니다. 그러나 Azure Stack을 배포한 경우 옵션을 사용자 지정한 경우이 창에서 값을 편집 해야 합니다. 예를 들어, 도메인 접미사를 사용 하는 경우 *mycloud.com*, Azure Stack Azure Resource Manager 끝점을 변경 해야 *management.region.mycloud.com*합니다. 귀하의 정보를 확인 한 후 클릭 **다음**합니다.

    ![Azure Stack 클라우드 정보][2]

7. 다음 페이지에서

   1. 클릭 합니다 **Connect** 단추 옆에 **Azure Stack 구독** 상자입니다.
        * Azure Active Directory (Azure AD)를 사용 하는 경우 Azure AD 관리자 계정 및 Azure Stack을 배포할 때 제공한 암호를 입력 합니다. 클릭 **로그인**합니다.
        * Active Directory Federation Services (AD FS)를 사용 하는 경우에 관리자 계정을 제공 합니다. 예를 들어 *cloudadmin@azurestack.local*입니다. 암호를 입력 하 고 클릭 **로그인**합니다.
   2. 에 **Azure Stack 구독** 상자를 선택 합니다 **공급자 구독 기본**.
   3. 에 **Azure Stack 위치** 상자에서 배포 하는 지역에 해당 하는 위치를 선택 합니다. 예를 들어 선택 **로컬** 경우 Azure Stack 개발 키트를 배포 합니다.
   4. 기존 App Service 배포 감지 되 면 다음 리소스 그룹 및 저장소 계정은 채울 되며 회색으로 표시 합니다.
   5. 클릭 **다음** 업그레이드 요약을 검토 합니다.

    ![검색 하는 app Service 설치][3]

8. 요약 페이지에서
   1. 선택 사항을 확인 합니다. 변경 하려면 사용 합니다 **이전** 이전 페이지를 방문 하는 단추입니다.
   2. 구성이 올바른 경우 확인란을 선택 합니다.
   3. 업그레이드를 시작 하려면 **다음**합니다.

       ![App Service 업그레이드 요약][4]

9. 업그레이드 진행률 페이지:
    1. 업그레이드 진행률을 추적 합니다. Azure Stack에서 App Service의 업그레이드 기간 배포할 역할 인스턴스의 수에 따라 달라 집니다.
    2. 업그레이드가 성공적으로 완료 되 면 클릭 **종료**합니다.

        ![App Service 업그레이드 진행률][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>다음 단계

또한에서 사용할 수 있는 다른 [서비스 (PaaS) 플랫폼](azure-stack-tools-paas-services.md)합니다.

* [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)
