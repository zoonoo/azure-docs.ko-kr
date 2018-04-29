---
title: 퀵 스타트 - Azure Portal로 첫 번째 Azure Container Instances 컨테이너 만들기
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Container Instances에 컨테이너를 배포합니다
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 34f1edf441a18e22bcbef0c827ab4301f97c1aa1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>빠른 시작: Azure Container Instances에서 첫 번째 컨테이너 만들기

Azure Container Instances를 통해 Azure에서 컨테이너를 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure에서 컨테이너를 만들고 공용 IP 주소를 사용하여 인터넷에 공개합니다. 이 작업은 Azure Portal을 사용하여 완료됩니다. 몇 번 클릭하면 브라우저에 이 내용이 표시됩니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-portal-07]

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal ( http://portal.azure.com ) 에 로그인합니다.

## <a name="create-a-container-instance"></a>컨테이너 인스턴스 만들기

**리소스 만들기** > **컨테이너** > **Azure Container Instances**를 선택합니다.

![Azure Portal에서 새 컨테이너 인스턴스를 만들기 시작][aci-portal-01]

**컨테이너 이름**, **컨테이너 이미지** 및 **리소스 그룹** 텍스트 상자에 다음 값을 입력합니다. 다른 값은 기본값을 그대로 두고 **확인**을 클릭합니다.

* 컨테이너 이름: `mycontainer`
* 컨테이너 이미지: `microsoft/aci-helloworld`
* 리소스 그룹: `myResourceGroup`

![Azure Portal에서 새 컨테이너 인스턴스의 기본 설정 구성][aci-portal-03]

Azure Container Instances에서는 Windows 및 Linux 컨테이너를 모두 만들 수 있습니다. 이 퀵 스타트에서는 이전 단계에서 Linux 기반 컨테이너(`microsoft/aci-helloworld`)를 지정했기 때문에 **Linux** 기본 설정에 대해 알아봅니다.

**구성**에서 다른 설정은 기본값을 그대로 두고 **확인**을 클릭하여 구성의 유효성 검사를 실행합니다.

![Azure Portal에서 새 컨테이너 인스턴스 구성][aci-portal-04]

유효성 검사가 완료되면 컨테이너 설정의 요약이 표시됩니다. **확인**을 선택하여 컨테이너 배포 요청을 제출합니다.

![Azure Portal에서 새 컨테이너 인스턴스의 설정 요약][aci-portal-05]

배포가 시작되면 포털 대시보드에 배포 진행 상황을 나타내는 타일이 배치됩니다. 배포가 완료되면 이 타일이 업데이트되어 새 **mycontainer myc1** 컨테이너 그룹을 표시합니다.

![Azure Portal에서 새 컨테이너 인스턴스를 만들기 진행 상황][aci-portal-08]

**mycontainer-myc1** 컨테이너 그룹을 선택하여 컨테이너 그룹 속성을 표시합니다. 컨테이너 그룹의 **IP 주소**와 컨테이너 **상태**를 기록해 둡니다.

![Azure Portal의 컨테이너 그룹 개요][aci-portal-06]

컨테이너가 **실행** 상태가 되면 이전 단계에서 기록해 둔 IP 주소로 이동하여 새 컨테이너에서 호스팅되는 응용 프로그램을 표시합니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-portal-07]

## <a name="delete-the-container"></a>컨테이너 삭제
컨테이너를 완료하는 경우 **mycontainer-myc1** 컨테이너 그룹을 선택한 다음 **삭제**를 클릭합니다.

![Azure Portal에서 컨테이너 인스턴스 삭제][aci-portal-09]

확인 대화 상자를 시작하고 메시지가 표시되면 **예**를 선택합니다.

![Azure Portal에서 컨테이너 인스턴스의 삭제 확인][aci-portal-10]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
<!--[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png-->
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 공용 Docker Hub 리포지토리의 이미지에서 Azure Container Instance를 만들었습니다. 컨테이너를 직접 빌드하고 Azure Container Registry를 사용하여 Azure Container Instances에 배포하려면 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)