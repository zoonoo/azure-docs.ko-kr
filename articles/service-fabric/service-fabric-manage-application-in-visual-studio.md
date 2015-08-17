<properties
   pageTitle="Visual Studio에서 서비스 패브릭 응용 프로그램 관리"
   description="Visual Studio를 통해 Microsoft Azure 서비스 패브릭 응용 프로그램 및 서비스를 관리할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# Visual Studio에서 서비스 패브릭 응용 프로그램 관리

Visual Studio를 통해 Microsoft Azure 서비스 패브릭 응용 프로그램 및 서비스를 관리할 수 있습니다. [개발 환경을 설정](../service-fabric-setup-your-development-environment)한 후, Visual Studio를 사용하여 로컬 개발 클러스터에서 서비스 패브릭 응용 프로그램을 만들거나 서비스를 추가하거나 응용 프로그램의 패키징, 등록 및 배포를 수행할 수 있습니다.

서비스 패브릭 응용 프로그램을 관리하려면 솔루션 탐색기에서 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭합니다.

![응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하여 서비스 패브릭 응용 프로그램 관리][manageservicefabric]

## 서비스 패브릭 응용 프로그램 배포

서비스 패브릭 응용 프로그램을 배포하는 작업은 다음 단계를 하나의 간단한 작업으로 통합합니다.

1. 응용 프로그램 패키지 만들기
2. 이미지 저장소에 응용 프로그램 패키지 업로드
3. 응용 프로그램 유형 등록
4. 실행 중인 모든 응용 프로그램 인스턴스 제거
5. 새 응용 프로그램 인스턴스 만들기

Visual Studio의 빌드 메뉴에서 솔루션 배포를 선택하여 응용 프로그램을 배포할 수 있습니다. 또한 **F5**를 누르면 응용 프로그램이 배포되고 모든 응용 프로그램 인스턴스에 디버거가 연결됩니다.


## 서비스 패브릭 응용 프로그램에 서비스 추가

응용 프로그램에 새 패브릭 서비스를 추가하면 기능을 확장할 수 있습니다. 응용 프로그램 패키지에 서비스가 포함되도록 하려면 **새 패브릭 서비스...** 메뉴 항목을 통해 서비스를 추가합니다.

![응용 프로그램에 새 패브릭 서비스 추가][newservice]

응용 프로그램에 추가할 서비스 패브릭 프로젝트 유형을 선택하고 서비스의 이름을 지정합니다. 사용할 서비스 유형을 결정하는 데 도움이 필요하면 [서비스를 위한 프레임워크 선택](service-fabric-choose-framework.md)을 참조하세요.

![응용 프로그램에 추가할 패브릭 서비스 프로젝트 유형 선택][addserviceproject]

새 서비스가 솔루션 및 기존 응용 프로그램 패키지에 추가됩니다. 서비스 참조 및 기본 서비스 인스턴스는 응용 프로그램 매니페스트에 추가됩니다. 다음 번에 응용 프로그램을 배포할 때 서비스가 만들어지고 시작됩니다.

![새 서비스가 응용 프로그램 매니페스트에 추가됩니다.][newserviceapplicationmanifest]

## 서비스 패브릭 응용 프로그램 패키징

응용 프로그램 및 해당 서비스를 클러스터에 배포하기 위해서는 응용 프로그램 패키지를 만들어야 합니다. 패키지는 응용 프로그램 매니페스트, 서비스 매니페스트 및 특정 레이아웃에서 필요한 기타 파일로 구성됩니다. Visual Studio는 응용 프로그램 프로젝트의 폴더인 'pkg' 디렉터리에서 패키지를 관리합니다. **패키지**를 클릭하면 응용 프로그램 패키지를 만들거나 업데이트할 수 있습니다. 사용자 지정 Powershell 스크립트를 사용하여 응용 프로그램을 배포하는 경우 이 작업을 수행할 수 있습니다.

## 응용 프로그램 제거

서버 탐색기를 사용하여 로컬 클러스터에서 응용 프로그램을 제거할 수 있습니다. 이렇게 하면 위에서 설명한 배포 단계를 되돌릴 수 있습니다.

1. 실행 중인 모든 응용 프로그램 인스턴스 제거
2. 응용 프로그램 유형 등록 해제
3. 이미지 저장소에서 응용 프로그램 패키지 제거

![응용 프로그램 제거](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

- [서비스 패브릭 응용 프로그램 모델](service-fabric-application-model.md)
- [서비스 패브릭 응용 프로그램 배포](service-fabric-deploy-remove-applications.md)
- [서비스 패브릭 응용 프로그램 디버깅](service-fabric-debugging-your-application.md)
- [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png

<!---HONumber=August15_HO6-->