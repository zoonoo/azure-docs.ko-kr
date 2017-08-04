---
title: "Party 클러스터에 Azure Service Fabric 응용 프로그램 배포 | Microsoft Docs"
description: "Party 클러스터에 응용 프로그램을 배포하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/03/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 1fde062d9673a48698b269d79e009c7b179e5934
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Azure에서 Party 클러스터에 응용 프로그램 배포
이 자습서는 Azure에서 Party 클러스터에 Azure Service Fabric 응용 프로그램을 배포하는 방법을 보여 줍니다.

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Visual Studio를 사용하여 원격 클러스터에 응용 프로그램 배포
> * Service Fabric Explorer를 사용하여 클러스터에서 응용 프로그램 제거

이 자습서는 세 개의 문서로 구성되며 이 문서는 시리즈 중 두 번째입니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Visual Studio 2017을 설치](https://www.visualstudio.com/)하고 **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드를 설치합니다.
- [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.

## <a name="set-up-a-party-cluster"></a>Party 클러스터 설정
Party 클러스터는 평가판으로, Azure에서 호스트되고 Service Fabric 팀이 실행하는 제한 시간 Service Fabric 클러스터입니다. 여기서 누구나 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있습니다. 평가판으로 제공됩니다.

Party 클러스터에 대한 액세스 권한을 얻으려면 http://aka.ms/tryservicefabric 사이트로 이동하고 지침에 따라 클러스터에 대한 액세스 권한을 얻을 수 있습니다. Party 클러스터에 대한 액세스 권한을 얻으려면 Facebook 또는 GitHub 계정이 필요합니다.

> [!NOTE]
> Party 클러스터는 보호되지 않으므로 응용 프로그램과 그 안에 있는 데이터는 다른 사람에게 표시될 수 있습니다. 다른 사람이 보기를 원하지 않는 항목은 배포하지 마세요. 모든 세부 사항은 사용 약관을 읽어 보아야 합니다.

## <a name="make-your-application-ready-for-deployment"></a>응용 프로그램 배포 준비
ASP.NET Core web api 서비스는 이 응용 프로그램에 대한 프런트 엔드로 작동하고 외부 트래픽을 허용하므로 이 서비스를 고정된 잘 알려진 포트에 바인딩하려고 합니다. 서비스 **ServiceManifest.xml** 파일에서 포트를 지정합니다.

1. 솔루션 탐색기에서 **WebAPIFrontEnd->PackageRoot->ServiceManifest.xml**을 엽니다.
2. 기존 **끝점** 요소의 **포트** 특성을 **80**으로 변경하고 변경 내용을 저장합니다.

## <a name="deploy-the-app-to-the-azure"></a>Azure에 앱 배포
이제 응용 프로그램이 준비되면 Visual Studio에서 Party 클러스터에 직접 배포할 수 있습니다.

1. 솔루션 탐색기에서 **MyApplication**을 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

    ![[게시] 대화 상자](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. **연결 끝점** 필드에 Party 클러스터의 연결 끝점을 입력하고 **게시**를 클릭합니다.

    게시가 완료되면 브라우저를 통해 요청을 응용 프로그램에 보낼 수 있습니다.

3. 기본 설정 브라우저를 열고, 클러스터 주소를 입력하며(포트 정보가 없는 연결 끝점. 예를 들어 win1kw5649s.westus.cloudapp.azure.com), url에 `/api/values`를 추가합니다.

    이제 응용 프로그램을 로컬로 실행할 때와 동일한 결과가 표시됩니다.

    ![클러스터에서 API 응답](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer를 사용하여 클러스터에서 응용 프로그램 제거
Service Fabric Explorer는 Service Fabric 클러스터에서 응용 프로그램을 탐색하고 관리하는 그래픽 사용자 인터페이스입니다.

Party 클러스터에 배포한 응용 프로그램을 제거하려면

1. Party 클러스터 등록 페이지에 제공된 링크를 사용하여 Service Fabric Explorer로 이동합니다. 예를 들어 http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html입니다.

2. Service Fabric Explorer의 왼쪽 트리 뷰에서 **fabric://MyApplication** 노드로 이동합니다.

3. 오른쪽 **Essentials** 창에서 **작업** 단추를 클릭하고 **응용 프로그램 삭제**를 선택합니다. 클러스터에서 실행 중인 응용 프로그램의 인스턴스를 제거하는 응용 프로그램 인스턴스 삭제를 확인합니다.

![Service Fabric Explorer에서 응용 프로그램 삭제](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

응용 프로그램은 Service Fabric 클러스터에서 응용 프로그램 형식으로 배포되므로 클러스터 내에서 실행되는 응용 프로그램의 여러 인스턴스 및 버전을 포함할 수 있습니다. 실행 중인 응용 프로그램 인스턴스를 제거한 후에는 형식도 제거하여 배포 정리를 완료할 수 있습니다.

Service Fabric에서 응용 프로그램 모델에 대한 자세한 내용은 [Service Fabric에서 응용 프로그램 모델링](service-fabric-application-model.md)을 참조하세요.

1. 트리 뷰에서 **MyApplicationType** 노드로 이동합니다.

2. 오른쪽 **Essentials** 창에서 **작업** 단추를 클릭하고 **프로비전 해제 형식**을 선택합니다. 응용 프로그램 형식의 프로비전 해제를 확인합니다.

![Service Fabric Explorer에서 응용 프로그램 형식 프로비전 해제](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

이제 자습서가 완료되었습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Visual Studio를 사용하여 원격 클러스터에 응용 프로그램 배포
> * Service Fabric Explorer를 사용하여 클러스터에서 응용 프로그램 제거

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Visual Studio Team Services를 사용하여 연속 통합 설정](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
