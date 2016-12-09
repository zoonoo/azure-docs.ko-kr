---
title: "Jenkins용 Azure Container Service 플러그 인 | Microsoft Docs"
description: "Jenkins용 Azure Container Service 플러그 인을 사용하여 Azure Container Service 클러스터 서비스에 docker 컨테이너를 배포합니다."
services: container-service
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
tags: azure-container-service, jenkins
keywords: "Azure, 컨테이너, Jenkins"
ms.assetid: dd719bda-6d1f-452b-a918-9a3aa9302107
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 435262867e8ead23d1453e8a2171f1c215fc5205


---
# <a name="azure-container-service-plugin-for-jenkins"></a>Jenkins용 Azure Container Service 플러그 인
Jenkins용 Azure Container Service 플러그 인을 사용하면 [Marathon]을 사용하는 [Docker] 컨테이너를 [Mesosphere의 DC/OS(데이터 센터 운영 체제)][mesosphere] 또는 [Apache Mesos][mesos]와 같은 분산 처리 시스템이 실행되는 Azure Container Cluster로 간단히 배포할 수 있습니다.

이 자습서에서는 Jenkins용 Azure Container Service 플러그 인을 설치하는 방법과 Marathon 및 DC/OS orchestrator로 Azure Container Service를 만들고 Marathon을 사용하여 Docker 컨테이너를 클러스터에 배포하는 Jenkins 프로젝트를 구성하는 방법을 보여 줍니다. Azure Container Service 클러스터가 없으면 Jenkins는 컨테이너 클러스터를 만들고 Marathon을 사용하여 클러스터에 docker 컨테이너를 배포합니다. 그렇지 않으면 Jenkins는 Marathon을 사용하여 기존 Azure Container Service 클러스터에 docker 컨테이너를 배포합니다.

Azure Container Service에 대 한 자세한 내용은 [Azure Container Service 소개][acs-intro] 문서를 참조하세요.

### <a name="prerequisites"></a>필수 조건
이 문서의 단계를 시작하기 전에 클라이언트 응용 프로그램을 등록하고 권한을 부여한 다음, 인증 중에 Azure Active Directory로 전송될 클라이언트 ID 및 클라이언트 암호를 검색해야 합니다. 이러한 필수 구성 요소에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Active Directory와 응용 프로그램 통합][integrate-apps-with-AAD]
* [클라이언트 앱 등록][register-client-app]

또한 다음 URL에서 **azure-acs-plugin.hpi** 파일을 다운로드해야 합니다.

* [https://github.com/Microsoft/azure-acs-plugin/tree/master/install](https://github.com/Microsoft/azure-acs-plugin/tree/master/install)

## <a name="how-to-install-the-azure-container-service-plugin-for-jenkins"></a>Jenkins용 Azure Container Service 플러그 인을 설치하는 방법
1. [GitHub에서 **azure-acs-plugin.hpi** 파일 다운로드][azure-acs-plugin-install]
2. Jenkins 대시보드에 로그인합니다.
3. 대시보드에서 **Manage Jenkins**를 클릭합니다.
   
    ![Jenkins 관리][jenkins-dashboard]
4. **Manage Jenkins** 페이지에서 **플러그 인 관리**를 클릭합니다.
   
    ![플러그 인 관리][manage-jenkins]
5. **Advanced** 탭을 클릭하고 **Upload Plugin** 섹션에서 **Browse**를 클릭합니다. **Prerequisites**에서 **plugin.hpi-acs-azure** 파일을 다운로드한 위치로 이동한 후 파일을 선택한 다음 **Upload**를 클릭합니다.
   
    ![플러그 인 업로드][upload-plugin]
6. 필요한 경우에 Jenkins를 다시 시작합니다.

## <a name="configure-the-azure-container-service-plugin"></a>Azure Container Service 플러그 인 구성
1. Jenkins 대시보드에서 프로젝트 중 하나를 클릭합니다.
   
    ![프로젝트 선택][select-project]
2. 프로젝트의 페이지가 표시되면 왼쪽 메뉴에서 **Configure**를 클릭합니다.
   
    ![프로젝트 구성][configure-project]
3. **Post-build Actions** 섹션에서 **Add post-build action** 드롭다운 메뉴를 클릭하고 **Azure Container Service Configuration**을 선택합니다. 
   
    ![고급 옵션][advanced-options]
4. **Azure Container Service Configuration** 섹션이 나타나면 구독 ID, 클라이언트 ID, 클라이언트 암호 및 OAuth 2.0 토큰 끝점 정보를 입력합니다.
   
    ![Azure Container Service 구성][azure-container-service-config]
5. **ACS Profile Configuration** 섹션에서 사용자의 지역, DNS 이름 접두사, 에이전트 수, 에이전트 VM 크기, 관리자 사용자 이름, 마스터 개수 및 SSH RSA 공개 키를 입력합니다.
   
    ![ACS 프로필 구성][acs-profile-configuration]
6. **Marathon Profile Configuration** 섹션에서 Marathon 구성 파일의 경로, SSH RSA 개인 파일 경로 및 SSH RSA 개인 파일 암호를 입력합니다.
   
    ![Marathon 프로필 구성][marathon-profile-configuration]
7. **Save**를 클릭하여 프로젝트에 대한 설정을 저장합니다.
   
    ![프로젝트 저장][save-project]
8. 왼쪽 메뉴에서 **Build Now**를 클릭합니다.
   
    ![프로젝트 빌드][build-project]

빌드가 완료되면 빌드 콘솔 로그에서 로그를 사용할 수 있습니다.

> [!NOTE]
> Azure Container Service 클러스터가 없으면 Jenkins는 컨테이너 클러스터를 만들고 Marathon을 사용하여 클러스터에 docker 컨테이너를 배포합니다. 그렇지 않으면 Jenkins는 Marathon을 사용하여 기존 Azure Container Service 클러스터에 docker 컨테이너를 배포합니다.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>참고 항목
Azure Container Service 및 DC/OS 클러스터를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Container Service 소개][acs-intro]
* [Azure Container Service 클러스터 배포][acs-deploy]
* [Azure Container Service 클러스터에 연결][acs-connect]
* [웹 UI를 통해 ACS 컨테이너 관리][acs-webui-management]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

<!-- URL List -->

[azure-acs-plugin-install]: https://github.com/Microsoft/azure-acs-plugin/tree/master/install
[acs-intro]: ./container-service-intro.md
[acs-deploy]: ./container-service-deployment.md
[acs-connect]: ./container-service-connect.md
[acs-webui-management]: ./container-service-mesos-marathon-ui.md
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx

[Marathon]: https://mesosphere.github.io/marathon/
[Docker]: http://docker.io/
[mesosphere]: https://mesosphere.com/products/
[mesos]: https://mesos.apache.org/

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[jenkins-dashboard]: ./media/container-service-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]: ./media/container-service-plugin-for-jenkins/manage-jenkins.png
[search-plugins]: ./media/container-service-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/container-service-plugin-for-jenkins/install-plugin.png
[select-project]: ./media/container-service-plugin-for-jenkins/select-project.png
[configure-project]: ./media/container-service-plugin-for-jenkins/configure-project.png
[advanced-options]: ./media/container-service-plugin-for-jenkins/advanced-options.png
[azure-container-service-config]: ./media/container-service-plugin-for-jenkins/azure-container-service-configuration.png
[acs-profile-configuration]: ./media/container-service-plugin-for-jenkins/acs-profile-configuration.png
[marathon-profile-configuration]: ./media/container-service-plugin-for-jenkins/marathon-profile-configuration.png
[save-project]: ./media/container-service-plugin-for-jenkins/save-project.png
[build-project]: ./media/container-service-plugin-for-jenkins/build-project.png
[upload-plugin]: ./media/container-service-plugin-for-jenkins/upload-plugin.png



<!--HONumber=Nov16_HO3-->


