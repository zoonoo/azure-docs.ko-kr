---
title: Azure Service Fabric 클러스터에 기존 앱을 신속하게 배포
description: Azure Service Fabric 클러스터를 사용하여 Visual Studio에서 기존 Node.js 애플리케이션을 호스트합니다.
services: service-fabric
documentationcenter: nodejs
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: bd19aba68f8b847e8f4800d348197f9c2b1c1289
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428238"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Azure Service Fabric에서 Node.js 애플리케이션 호스트

이 빠른 시작을 통해 Azure에서 실행되는 Service Fabric 클러스터에 기존 애플리케이션(이 예제에서는 Node.js)을 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [개발 환경을 설정](service-fabric-get-started.md)하도록 합니다. Service Fabric SDK 및 Visual Studio 2019 또는 2015 설치를 포함 합니다.

또한 배포하기 위해 기존 Node.js 애플리케이션이 필요합니다. 이 빠른 시작은 [여기][download-sample]에서 다운로드할 수 있는 간단한 Node.js 웹 사이트를 사용합니다. 다음 단계에서 프로젝트를 만든 후 `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` 폴더에 이 파일을 추출합니다.

Azure 구독이 아직 없는 경우 [체험 계정][create-account]을 만듭니다.

## <a name="create-the-service"></a>서비스 만들기

**관리자** 권한으로 Visual Studio를 시작합니다.

`CTRL`+`SHIFT`+`N`를 사용하여 프로젝트 만들기

**새 프로젝트** 대화 상자에서 **클라우드 &gt; Service Fabric 애플리케이션**을 선택합니다.

애플리케이션의 이름을 **MyGuestApp**으로 지정하고 **확인**을 누릅니다.

>[!IMPORTANT]
>Node.js는 Windows에 있는 경로에 대한 260자 제한을 쉽게 초과할 수 있습니다. **c:\code\svc1**과 같은 프로젝트 자체에 짧은 경로를 사용합니다. 경우에 따라 **[이러한 지침](https://stackoverflow.com/a/41687101/1664231)** 을 따라 Windows 10에서 긴 파일 경로를 활성화할 수 있습니다.
   
![Visual Studio의 새 프로젝트 대화 상자][new-project]

다음 대화 상자에서 모든 형식의 Service Fabric 서비스를 만들 수 있습니다. 이 빠른 시작에서 **게스트 실행 파일**을 선택합니다.

서비스 이름을 **MyGuestService**로 지정하고 다음과 같은 값으로 오른쪽에 있는 옵션을 설정합니다.

| 설정                   | 값 |
| ------------------------- | ------ |
| 코드 패키지 폴더       | _&lt;Node.js 앱을 포함한 폴더&gt;_ |
| 코드 패키지 동작     | 프로젝트에 폴더 내용 복사 |
| 프로그램                   | node.exe |
| 인수                 | server.js |
| 작업 폴더            | CodePackage |

**확인**을 누릅니다.

![Visual Studio의 새 서비스 대화 상자][new-service]

Visual Studio는 애플리케이션 프로젝트 및 작업자 서비스 프로젝트를 만들고 솔루션 탐색기에 표시합니다.

응용 프로그램 프로젝트 (**MyGuestApp**) 코드를 직접 포함 하지 않습니다. 프로젝트에는 서비스 프로젝트의 집합을 참조합니다. 또한 다른 세 가지 유형의 콘텐츠를 포함 합니다.

* **게시 프로필**  
다양한 환경에 대한 도구 기본 설정입니다.

* **스크립트**  
애플리케이션을 배포/업그레이드하기 위한 PowerShell 스크립트입니다.

* **애플리케이션 정의**  
*ApplicationPackageRoot*에서 애플리케이션 매니페스트를 포함합니다. 관련된 애플리케이션 매개 변수 파일은 *ApplicationParameters*에 있으며 애플리케이션을 정의하고 지정된 환경에 대해 해당 애플리케이션을 구체적으로 구성할 수 있습니다.
    
서비스 프로젝트의 내용에 대한 개요는 [Reliable Services 시작](service-fabric-reliable-services-quick-start.md)을 참조하세요.

## <a name="set-up-networking"></a>네트워킹 설정

배포하는 예제 Node.js 앱은 포트 **80**을 사용하므로 Service Fabric에 해당 포트를 노출하도록 지시합니다.

프로젝트에서 **ServiceManifest.xml** 파일을 엽니다. 매니페스트의 맨 아래에 있는지를 `<Resources> \ <Endpoints>` 이미 정의 된 항목입니다. 해당 항목을 수정하여 `Port`, `Protocol` 및 `Type`을 추가합니다. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Deploy to Azure

키를 누르면 **F5** 을 로컬 클러스터에 배포 된 프로젝트를 실행 합니다. 하지만 대신 Azure에 배포해보겠습니다.

프로젝트를 마우스 오른쪽 단추로 클릭하고 대화 상자를 여는 **게시...** 를 선택하여 Azure에 게시할 수 있습니다.

![Service Fabric 서비스의 Azure 대화 상자에 게시][publish]

**PublishProfiles\Cloud.xml** 대상 프로필을 선택합니다.

이전에 수행하지 않은 경우 배포할 Azure 계정을 선택합니다. 아직 없는 경우 [하나에 등록][create-account]합니다.

**연결 엔드포인트**에서 배포할 Service Fabric 클러스터를 선택합니다. 없다면 하나를 선택 **&lt;새 클러스터 만들기... &gt;** 를 Azure portal에 웹 브라우저 창을 엽니다. 자세한 내용은 [포털에서 클러스터 만들기](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal)를 참조하세요. 

Service Fabric 클러스터를 만들 때 **사용자 지정 엔드포인트** 설정을 **80**으로 설정해야 합니다.

![사용자 지정 엔드포인트가 있는 Service Fabric 노드 형식 구성][custom-endpoint]

새 Service Fabric 클러스터 만들기를 완료하려면 다소 시간이 걸립니다. Service Fabric 클러스터를 만들면 게시 대화 상자로 다시 이동하고 **&lt;새로 고침&gt;** 을 선택합니다. 새 클러스터가 드롭다운 상자에 나열되면 선택합니다.

**게시**를 눌러서 배포가 끝나기를 기다립니다.

몇 분이 걸릴 수 있습니다. 배포가 완료된 후에 완벽하게 애플리케이션을 사용할 수 있는 데 몇 분이 걸릴 수 있습니다.

## <a name="test-the-website"></a>웹 사이트 테스트

서비스가 게시된 후에 웹 브라우저에서 테스트합니다. 

먼저 Azure Portal을 열고 Service Fabric 서비스를 찾습니다.

서비스 주소의 개요 블레이드를 확인합니다. _클라이언트 연결 엔드포인트_ 속성의 도메인 이름을 사용합니다. 예: `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Azure Portal에서 Service Fabric 개요 블레이드][overview]

표시 되는이 주소로 이동 하 여 `HELLO WORLD` 응답 합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

이러한 리소스에 대 한 청구 되는이 빠른 시작에서 만든 리소스를 모두 삭제 해야 합니다.

## <a name="next-steps"></a>다음 단계
[게스트 실행 파일](service-fabric-guest-executables-introduction.md)에 대해 자세히 알아보세요.

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
