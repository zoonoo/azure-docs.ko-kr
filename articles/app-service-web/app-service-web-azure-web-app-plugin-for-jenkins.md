---
title: "Jenkins Continuous Integration과 함께 Azure 웹앱 플러그 인을 사용하는 방법 | Microsoft Docs"
description: "Jenkins Continuous Integration과 함께 Azure 웹앱 플러그 인을 사용하는 방법을 설명합니다."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: be6c4e62-da76-44f6-bb00-464902734805
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6f18c9b802d6fa18c4f50d3a537003eaaf25d14


---
# <a name="how-to-use-the-azure-web-app-plugin-with-jenkins-continuous-integration"></a>Jenkins Continuous Integration과 함께 Azure 웹앱 플러그 인을 사용하는 방법
Jenkins용 Azure 웹앱 플러그 인을 사용하면 분산 빌드를 실행할 때 Azure에서 웹앱을 쉽게 만들고 웹앱에 WAR 파일을 배포할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
이 문서의 단계를 시작하기 전에 클라이언트 응용 프로그램을 등록하고 권한을 부여한 다음, 인증 중에 Azure Active Directory로 전송될 클라이언트 ID 및 클라이언트 암호를 검색해야 합니다. 이러한 필수 구성 요소에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Active Directory와 응용 프로그램 통합][integrate-apps-with-AAD]
* [클라이언트 앱 등록][register-client-app]

또한 다음 URL에서 **azure-webapp-plugin.hpi** 파일을 다운로드해야 합니다.

* [https://github.com/Microsoft/azure-webapp-plugin/tree/master/install](https://github.com/Microsoft/azure-webapp-plugin/tree/master/install)

## <a name="how-to-install-the-azure-web-app-plugin-for-jenkins"></a>Jenkins용 Azure 웹앱 플러그 인을 설치하는 방법
1. [GitHub에서 **azure-webapp-plugin.hpi** 파일 다운로드][azure-webapp-plugin-install]
2. Jenkins 대시보드에 로그인합니다.
3. 대시보드 내에서 **Manage Jenkins**를 클릭합니다.
   
    ![Jenkins 관리][jenkins-dashboard]
4. **Manage Jenkins** 페이지에서 **플러그 인 관리**를 클릭합니다.
   
    ![플러그 인 관리][manage-jenkins]
5. **Advanced** 탭을 클릭하고 **Upload Plugin** 섹션에서 **Browse**를 클릭합니다. **Prerequisites**에서 **azure-webapp-plugin.hpi** 파일을 다운로드한 위치로 이동한 후 파일을 선택한 다음 **Upload**를 클릭합니다.
   
    ![플러그 인 업로드][upload-plugin]
6. 필요한 경우에 Jenkins를 다시 시작합니다.

## <a name="configure-the-azure-web-app-plugin"></a>Azure 웹앱 플러그 인 구성
1. Jenkins 대시보드에서 프로젝트 중 하나를 클릭합니다.
   
    ![프로젝트 선택][select-project]
2. 프로젝트의 페이지가 표시되면 왼쪽 메뉴에서 **Configure**를 클릭합니다.
   
    ![프로젝트 구성][configure-project]
3. **Post-build Actions** 섹션에서 **Add post-build action** 드롭다운 메뉴를 클릭하고 **Azure Webapp Configuration**을 선택합니다. 
   
    ![고급 옵션][advanced-options]
4. **Azure Webapp Configuration** 섹션이 나타나면 **Azure Profile Configuration**에 **구독 ID**, **클라이언트 ID**, **클라이언트 암호** 및 **OAuth 2.0 토큰 끝점** 정보를 입력합니다.
   
    ![Azure 프로필 구성][azure-profile-configuration]
5. **Webapp Configuration** 섹션에서 **리소스 그룹 이름**, **위치**, **호스팅 계획 이름**, **웹앱 이름**, **Sku 이름**, **Sku 용량** 및 **War 파일 경로** 정보를 입력합니다.
   
    ![웹앱 구성][webapp-configuration]
6. **Save**를 클릭하여 프로젝트에 대한 설정을 저장합니다.
   
    ![프로젝트 저장][save-project]
7. 왼쪽 메뉴에서 **Build Now**를 클릭합니다.
   
    ![프로젝트 빌드][build-project]

> [!NOTE]
> 웹앱 컨테이너는 웹앱 컨테이너가 아직 없는 경우에먼 만들어집니다.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>참고 항목
Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

Jenkins용 Azure 웹앱 플러그 인에 대한 자세한 내용은 GitHub의 [Azure 웹앱 플러그 인] 프로젝트를 참조하세요.

<!-- URL List -->

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[Azure 웹앱 플러그 인]: https://github.com/Microsoft/azure-webapp-plugin
[azure-webapp-plugin-install]: https://github.com/Microsoft/azure-webapp-plugin/tree/master/install

<!-- IMG List -->

[jenkins-dashboard]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/manage-jenkins.png
[upload-plugin]:     ./media/app-service-web-azure-web-app-plugin-for-jenkins/upload-plugin.png
[select-project]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/select-project.png
[configure-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/configure-project.png
[advanced-options]:  ./media/app-service-web-azure-web-app-plugin-for-jenkins/advanced-options.png
[azure-profile-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/azure-profile-configuration.png
[build-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/build-project.png
[save-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/save-project.png
[webapp-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/webapp-configuration.png



<!--HONumber=Nov16_HO3-->


