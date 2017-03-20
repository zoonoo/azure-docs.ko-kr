---
title: "Azure Service Fabric용 Eclipse 플러그 인 시작 | Microsoft Docs"
description: "Azure Service Fabric용 Eclipse 플러그 인 시작"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Service Fabric Java 응용 프로그램 개발용 Eclipse 플러그 인 시작
Eclipse는 가장 많이 사용되는 Java 개발자를 위한 IDE 중 하나입니다. 이 문서에서는 Service Fabric 작업을 수행하기 위해 Eclipse 개발 환경을 설정하는 방법에 대해 설명합니다. 이 문서는 플러그 인을 설치하고 서비스 패브릭 응용 프로그램을 만들며 Service Fabric 응용 프로그램을 로컬 또는 원격 Service Fabric 클러스터에 배포하는 데 도움이 됩니다.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Eclipse Neon에 Service Fabric 플러그 인 설치 또는 업데이트
Service Fabric은 Java 서비스를 빌드하고 배포하는 프로세스를 간소화할 수 있는 **Java 개발자를 위한 Eclipse IDE**에 대한 플러그 인을 제공합니다.

1. 최신 Eclipse **Neon** 및 Buildship 버전(1.0.17 이상)을 설치하도록 합니다. ``Help => Installation Details``를 선택하여 설치된 구성 요소의 버전을 확인할 수 있습니다. [여기][buildship-update]에 있는 지침을 사용하여 Buildship을 업데이트할 수 있습니다. Eclipse Neon이 최신 버전인지 확인하고 업데이트하려면 ``Help => Check for Updates``로 이동하면 됩니다.

2. Service Fabric 플러그 인을 설치하려면 ``Help => Install New Software...``를 선택합니다.
  1. "사용" 텍스트 상자에 ``http://dl.windowsazure.com/eclipse/servicefabric``을 입력합니다.
  2. 추가를 클릭합니다.

  ![Service Fabric용 Eclipse Neon 플러그 인][sf-eclipse-plugin-install]

  3. Service Fabric 플러그 인을 선택하고 [다음]을 클릭합니다.
  4. 설치를 계속하고 최종 사용자 라이선스 규약에 동의합니다.

Service Fabric Eclipse 플러그 인이 이미 설치된 경우 최신 버전인지 확인합니다. ``Help => Installation Details``에서 추가로 업데이트할 수 있는지 확인할 수 있습니다. 그런 다음 설치된 플러그 인 목록에서 서비스 패브릭을 검색하고 업데이트를 클릭합니다. 보류 중인 업데이트가 있으면 가져와 설치합니다.

> [!NOTE]
> Eclipse에서 Service Fabric Eclipse 플러그 인을 설치 또는 업데이트하는 데 시간이 많이 소요된다면 Eclipse가 모든 새로운 변경 사항에 대한 메타데이터를 가져오려고 시도할 때마다 Eclipse 인스턴스에 등록된 모든 업데이트 사이트에 적용되기 때문입니다. 따라서 이 작업을 보다 신속히 하려는 작은 트릭으로, `Available Software Sites`로 이동하여 Service Fabric 플러그인 위치 `http://dl.windowsazure.com/eclipse/servicefabric`을 가리키는 것 이외의 모든 항목을 선택 취소합니다.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Eclipse를 사용하여 Service Fabric 응용 프로그램 만들기

1. ``File => New => Other``로 이동합니다. ``Service Fabric Project``를 선택합니다. ``Next``을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 1][create-application/p1]

2. 프로젝트에 이름을 지정합니다. ``Next``을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 2][create-application/p2]

3. 사용 가능한 템플릿 목록에서 서비스 템플릿을 선택합니다(작업자, 상태 비저장, 컨테이너 또는 게스트 실행 파일). ``Next``을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 3][create-application/p3]

4. 이 페이지에서 서비스 이름 및/또는 관련 서비스 세부 정보를 입력하고 ``Finish``을 클릭합니다.

    ![Service Fabric 새 프로젝트 페이지 4][create-application/p4]

5. 첫 번째 Service Fabric 프로젝트를 만드는 경우 Service Fabric 관점을 설정할지 묻는 메시지가 표시되면 ``yes``를 선택하여 진행하세요.

    ![Service Fabric 새 프로젝트 페이지 5][create-application/p5]

6. 성공적으로 만들어지면 프로젝트는 다음과 같습니다.

    ![Service Fabric 새 프로젝트 페이지 6][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Eclipse를 사용하여 Service Fabric 응용 프로그램 빌드 및 배포

* 위에서 방금 만든 Service Fabric 응용 프로그램을 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 ``Service Fabric`` 옵션을 선택합니다. 그러면 여러 옵션이 있는 하위 메뉴가 표시됩니다. 다음과 유사합니다.

    ![Service Fabric 마우스 오른쪽 클릭 메뉴][publish/RightClick]

  빌드, 다시 빌드 및 정리에 대한 옵션을 클릭하면 원하는 작업이 수행됩니다.
  - ``Build Application``은 정리하지 않고 응용 프로그램을 빌드합니다.
  - ``Rebuild Application``은 응용 프로그램의 정리-빌드를 수행합니다.
  - ``Clean Application``은 빌드한 아티팩트의 응용 프로그램을 정리합니다.


* 이 메뉴에서 응용 프로그램을 배포, 배포 취소 및 게시하도록 선택할 수도 있습니다.
  - ``Deploy Application``은 로컬 클러스터에 배포합니다.
  - ``Publish Application...``은 ``Local.json`` 및 ``Cloud.json`` 중에서 선택하려는 게시 프로필을 묻습니다. 이러한 JSON 파일은 로컬 또는 클라우드 (Azure) 클러스터에 연결하는 데 필요한 정보(예: 연결 끝점 및 보안 정보)를 저장하는 데 사용됩니다.

  ![Service Fabric 마우스 오른쪽 클릭 메뉴][publish/Publish]

* Eclipse 실행 구성을 사용하여 Service Fabric 응용 프로그램을 배포할 수 있는 다른 방법이 있습니다.

  1. ``Run => Run Configurations``을 선택합니다. ``Grade Project`` 아래에서 ``ServiceFabricDeployer`` 실행 구성을 선택합니다.
  2. 오른쪽 창의 ``Arguments`` 탭 아래에서 ``publishProfile``로 **로컬** 또는 **클라우드**를 지정합니다. 기본 설치는 **로컬**입니다. 원격/클라우드 클러스터에 배포하는 경우 **클라우드**를 선택합니다.
  3. `Local.json` 또는 `Cloud.json`를 적절하게 편집하여 끝점 세부 정보 및 보안 자격 증명(있는 경우) 등 적절한 정보를 게시 프로필에 채우도록 합니다.
  4. ``Grade Project`` 아래 오른쪽 창의 ``Working Directory``는 배포하려는 응용 프로그램을 가리키도록 합니다. 그렇지 않은 경우 ``Workspace...`` 단추를 클릭하고 원하는 응용 프로그램을 선택합니다.
  5. **적용**을 클릭한 다음 **실행**을 클릭합니다.

몇 분 내로 응용 프로그램을 빌드하고 배포합니다. Service Fabric Explorer에서 해당 상태를 모니터링할 수 있습니다.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric 응용 프로그램에 새 Service Fabric 서비스 추가

다음 단계에 따라 새 Service Fabric 서비스를 기존 Service Fabric 응용 프로그램에 추가할 수 있습니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 표시되는 상황에 맞는 메뉴에서 서비스를 추가하려면 'Service Fabric' 옵션을 선택합니다. 그러면 여러 옵션이 있는 하위 메뉴가 표시됩니다.

    ![Service Fabric 서비스 추가 페이지 1][add-service/p1]

2. `Add ServiceFabric Service` 옵션을 선택하면 프로젝트에 서비스를 추가하는 다음 단계 집합이 안내됩니다.
3. 프로젝트에 추가할 서비스 템플릿을 선택하고 [다음]을 클릭합니다.

    ![Service Fabric 서비스 추가 페이지 2][add-service/p2]

4. 서비스 이름(및 필요한 경우 기타 필요한 세부 정보)을 입력하고 아래 [서비스 추가] 단추를 클릭합니다.  

    ![Service Fabric 서비스 추가 페이지 3][add-service/p3]

5. 서비스가 성공적으로 추가되었으면 전체 프로젝트 구조는 아래와 같습니다.

    ![Service Fabric 서비스 추가 페이지 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Service Fabric Java 응용 프로그램 업그레이드

Service Fabric Eclipse 플러그 인을 사용하여 ``App1`` 프로젝트를 만들었고 이 플러그인을 사용하여 배포한 후 응용 프로그램 유형 ``App1AppicationType`` 및 응용 프로그램 버전 1.0인 ``fabric:/App1Application``이라는 응용 프로그램을 만들었다고 가정해 보겠습니다. 이제 중지하지 않고 응용 프로그램을 업그레이드하려고 합니다.

응용 프로그램을 변경하고 수정된 서비스를 다시 빌드합니다.  수정된 서비스의 매니페스트 파일(``ServiceManifest.xml``)을 업데이트된 버전의 서비스(및 필요에 따라 코드, 구성 또는 데이터)로 업데이트합니다. 또한 응용 프로그램의 매니페스트(``ApplicationManifest.xml``)를 응용 프로그램에 대한 업데이트된 버전 번호와 수정된 서비스로 수정합니다.  

Eclipse를 사용하여 응용 프로그램을 업그레이드하려면 다음 단계에 따라 중복 실행 구성을 만들고 이를 사용하여 응용 프로그램을 필요에 따라 업그레이드합니다.
1. ``Run => Run Configurations``을 선택합니다. 왼쪽 창에서 ``Grade Project``의 왼쪽에 있는 작은 화살표를 클릭합니다.
2. ``ServiceFabricDeployer``를 마우스 오른쪽 단추로 클릭하고 ``Duplicate``를 선택합니다. 이 구성에 ``ServiceFabricUpgrader``라는 새 이름을 지정합니다.
3. 오른쪽 패널의 ``Arguments`` 탭 아래에서 ``-Pconfig='deploy'``를 ``-Pconfig=upgrade``로 변경한 후 ``Apply``를 클릭합니다.
4. 이제 응용 프로그램 업그레이드를 위한 실행 구성을 만들어 저장했으므로 필요할 때 ``Run``할 수 있습니다. 이렇게 응용 프로그램 매니페스트 파일에서 최신 업데이트된 응용 프로그램 유형 버전 가져오기를 처리합니다.

이제 Service Fabric Explorer를 사용하여 응용 프로그램 업그레이드를 모니터링할 수 있습니다. 몇 분 후 응용 프로그램이 업데이트됩니다.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

