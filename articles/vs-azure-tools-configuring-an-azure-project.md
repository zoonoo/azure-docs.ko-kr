<properties
   pageTitle="Visual Studio에서 Azure 클라우드 서비스 프로젝트 구성 | Microsoft Azure"
   description="프로젝트의 요구 사항에 따라 Visual Studio에서 Azure 클라우드 서비스 프로젝트를 구성하는 방법을 알아보세요."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Visual Studio에서 Azure 클라우드 서비스 프로젝트 구성

프로젝트 요구 사항에 따라 Azure 클라우드 서비스 프로젝트를 구성할 수 있습니다. 다음 범주에 대해 프로젝트 속성을 설정할 수 있습니다.

- **Azure에 클라우드 서비스 게시**

  Azure에 배포된 기존 클라우드 서비스가 실수로 삭제되지 않도록 속성을 설정할 수 있습니다.

- **로컬 컴퓨터에서 클라우드 서비스 실행 또는 디버깅**

  사용할 서비스 구성을 선택하고 Azure 저장소 에뮬레이터를 시작할 것인지를 선택할 수 있습니다.

- **생성된 클라우드 서비스 패키지 유효성 검사**

  클라우드 서비스 패키지가 문제 없이 배포되도록 모든 경고를 오류로 처리할 수 있습니다. 이 경우 배포한 다음 문제가 발생했음을 확인할 수 있어 기다리는 시간이 줄어듭니다.

다음 그림은 클라우드 서비스를 로컬로 실행 또는 디버깅할 때 사용할 구성을 선택하는 방법을 보여줍니다. 그림과 같이, 이 창에서 필요한 프로젝트 속성을 설정할 수 있습니다.

![Microsoft Azure 프로젝트 구성](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## Azure 클라우드 서비스 프로젝트를 구성하려면

1. **솔루션 탐색기**에서 클라우드 서비스 프로젝트를 구성하려면 클라우드 서비스 프로젝트에 대한 바로 가기 메뉴를 열고 **속성**을 선택합니다.

  Visual Studio 편집기에 클라우드 서비스 프로젝트의 이름이 표시된 페이지가 나타납니다.

1. **개발** 탭을 선택합니다.

1. Azure의 기존 배포를 실수로 삭제하지 않으려면 기존 배포 목록을 삭제하기 전 메시지에서 **True**를 선택합니다.

1. 클라우드 서비스를 로컬로 실행 또는 디버깅할 때 사용할 서비스 구성을 선택하려면 **서비스 구성** 목록에서 서비스 구성을 선택합니다.

  >[AZURE.NOTE] 사용할 서비스 구성을 만들려면 서비스 구성 및 프로필을 관리하는 방법을 참조하세요. 역할에 대한 서비스 구성을 수정하려면 [Visual Studio를 사용하여 Azure 클라우드 서비스에 대한 역할을 구성하는 방법](vs-azure-tools-configure-roles-for-cloud-service.md)을 참조하세요.

1. 클라우드 서비스를 로컬로 실행 또는 디버깅할 경우 Azure 저장소 에뮬레이터를 시작하려면 **Azure 저장소 에뮬레이터 시작**에서 **True**를 선택합니다.

1. 패키지 유효성 검사 오류가 있을 경우 게시하지 않도록 하려면 **경고를 오류로 처리**에서 **True**를 선택합니다.

1. 웹 역할이 IIS Express에서 로컬로 시작될 때마다 동일한 포트를 사용하도록 하려면 **웹 프로젝트 포트 사용**에서 **True**를 선택합니다. 특정 웹 프로젝트에 대해 특정 포트를 사용하려면 웹 프로젝트에 대해 바로 가기 메뉴를 열고 **속성** 탭을 선택한 다음 **웹** 탭을 선택하고 **IIS Express** 섹션의 **프로젝트 Url** 설정에서 포트 번호를 변경합니다. 예를 들어 프로젝트 URL로 `http://localhost:14020`을 입력합니다.

1. 클라우드 서비스 프로젝트의 속성을 변경한 내용을 저장하려면 도구 모음에서 **저장** 단추를 선택합니다.

## 다음 단계

Visual Studio에서 Azure 클라우드 서비스 프로젝트를 구성하는 방법을 자세히 알아보려면 [여러 서비스 구성을 사용하여 Azure 프로젝트 구성](vs-azure-tools-multiple-services-project-configurations.md)을 참조하세요.

<!---HONumber=AcomDC_0817_2016-->