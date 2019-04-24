---
title: Visual Studio Code를 사용하여 Java Azure Service Fabric 애플리케이션 개발 | Microsoft Docs
description: 이 문서에서는 Visual Studio Code를 사용하여 Java Service Fabric 애플리케이션을 빌드, 배포 및 디버그하는 방법을 보여 줍니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 7f60371fb533526ef5bdb154d0c08dface9c0d1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393951"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Visual Studio Code를 사용하여 Java Service Fabric 애플리케이션 개발

[VS Code용 Service Fabric Reliable Services 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)을 사용하면 Windows, Linux 및 macOS 운영 체제에서 Java Service Fabric 애플리케이션을 쉽게 빌드할 수 있습니다.

이 문서에서는 Visual Studio Code를 사용하여 Java Service Fabric 애플리케이션을 빌드, 배포 및 디버그하는 방법을 보여 줍니다.

> [!IMPORTANT]
> Service Fabric Java 애플리케이션은 Windows 컴퓨터에서 개발할 수 있지만 Azure Linux 클러스터에만 배포할 수 있습니다. Windows에서는 Java 응용 프로그램을 디버그할 수 없습니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 VS Code, VS Code용 Service Fabric Reliable Services 확장 및 개발 환경에 필요한 모든 종속성을 설치했다고 가정합니다. 자세한 내용은 [시작](./service-fabric-get-started-vs-code.md#prerequisites)을 참조하세요.

## <a name="download-the-sample"></a>샘플 다운로드
이 문서에서는 [Service Fabric Java 응용 프로그램 빠른 시작 샘플 GitHub 리포지토리](https://github.com/Azure-Samples/service-fabric-java-quickstart)의 Voting 응용 프로그램을 사용합니다. 

개발 컴퓨터에 리포지토리를 복제하려면 터미널 창(Windows의 명령 창)에서 다음 명령을 실행합니다.

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>VS Code에서 응용 프로그램 열기

VS Code를 엽니다.  **작업 막대**에서 탐색기 아이콘을 클릭하고 **폴더 열기**를 클릭하거나 **파일 -> 폴더 열기**를 클릭합니다. 리포지토리를 복제한 폴더의 *./service-fabric-java-quickstart/Voting* 디렉터리로 이동한 후 **확인**을 클릭합니다. 작업 영역에는 아래 스크린샷에 표시된 것과 동일한 파일이 포함되어야 합니다.

![작업 영역의 Java Voting 응용 프로그램](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>애플리케이션 빌드

1. VS Code에서 (Ctrl + Shift + p)를 눌러 **명령 팔레트**를 엽니다.
2. **Service Fabric: Build Application** 명령을 검색하고 선택합니다. 빌드 출력이 통합된 터미널로 전송됩니다.

   ![VS Code의 Build Application 명령](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>로컬 클러스터에 애플리케이션 배포
애플리케이션이 빌드되면 로컬 클러스터에 배포할 수 있습니다. 

> [!IMPORTANT]
> Windows 컴퓨터에서는 로컬 클러스터로 Java 애플리케이션을 배포할 수 없습니다.

1. **명령 팔레트**에서 **Service Fabric: 애플리케이션 배포(Localhost) 명령**을 선택합니다. 설치 프로세스의 출력이 통합된 터미널로 전송됩니다.

   ![VS Code의 Deploy Application 명령](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. 배포가 완료되면 브라우저를 시작하고 `http://localhost:19080/Explorer`의 Service Fabric Explorer를 엽니다. 애플리케이션이 실행되고 있는 것을 확인할 수 있습니다. 다소 시간이 소요되니 기다려 주세요. 

   ![Service Fabric Explorer의 Voting 애플리케이션](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. 애플리케이션이 실행되고 있는지 확인한 후 브라우저를 시작하고 `http://localhost:8080` 페이지를 엽니다. 애플리케이션의 웹 프런트 엔드입니다. 항목을 추가하고 클릭하여 투표할 수 있습니다.

   ![브라우저의 Voting 애플리케이션](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. 클러스터에서 응용 프로그램을 제거 하려면 선택 하 여 **Service Fabric: 응용 프로그램 제거** 에서 명령을 합니다 **명령 팔레트**합니다. 제거 프로세스의 출력이 통합된 터미널로 전송됩니다. Service Fabric Explorer를 사용하여 응용 프로그램이 로컬 클러스터에서 제거되었는지 확인할 수 있습니다.

## <a name="debug-the-application"></a>애플리케이션 디버그
VS Code에서 응용 프로그램을 디버그할 때 응용 프로그램은 로컬 클러스터에서 실행되고 있어야 합니다. 그래야 코드에 중단점을 추가할 수 있습니다.

> [!IMPORTANT]
> Windows 컴퓨터에서는 Java 애플리케이션을 디버그할 수 없습니다.

VotingDataService 및 Voting 애플리케이션을 디버그할 수 있게 준비하려면 다음 단계를 수행합니다.

1. *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* 파일을 업데이트합니다.
6번 줄에서 명령을 주석 처리하고(# 사용) 파일 끝에 다음 명령을 추가합니다.

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. *Voting/VotingApplication/ApplicationManifest.xml* 파일을 업데이트합니다. **StatefulService** 요소에서 **MinReplicaSetSize** 및 **TargetReplicaSetSize** 특성을 "1"로 설정합니다.
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. **작업 막대**에서 디버그 아이콘을 클릭하여 VS Code에서 디버거 보기를 엽니다. 디버거 보기의 맨 위에 있는 톱니바퀴 아이콘을 클릭하고 드롭다운 메뉴에서 **Java**를 선택합니다. launch.json 파일이 열립니다. 

   ![VS Code 작업 영역의 디버그 아이콘](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. launch.json 파일에서 **디버그(연결)** 구성의 포트 값을 **8001**로 설정합니다. 파일을 저장합니다.

   ![launch.json에 대한 디버그 구성](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. 사용 하 여 로컬 클러스터에 응용 프로그램 배포는 **Service Fabric: 배포 응용 프로그램 (Localhost)** 명령입니다. 애플리케이션이 Service Fabric Explorer에서 실행되고 있는지 확인합니다. 애플리케이션을 디버그할 준비가 되었습니다.

중단점을 설정하려면 다음 단계를 수행합니다.

1. Explorer에서 */Voting/VotingDataService/src/statefulservice/VotingDataService.java* 파일을 엽니다. `addItem` 메서드의 `try` 블록에서 첫 번째 코드 줄에 중단점을 설정합니다(줄 80).
   
   ![Voting Data Service에서 중단점 설정](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > 코드의 실행 파일 줄에 중단점을 설정해야 합니다. 예를 들어, 메서드 선언, `try` 문 또는 `catch` 문에 설정된 중단점은 디버거에서 건너뜁니다.
2. 디버깅을 시작하려면 **작업 막대**에서 디버그 아이콘을 클릭하고 디버그 메뉴에서 **디버그(연결)** 구성을 선택한 후 실행 단추(녹색 화살표)를 클릭합니다.

   ![디버그(연결) 구성](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. 웹 브라우저에서 `http://localhost:8080`으로 이동합니다. 텍스트 상자에 새 항목을 입력하고 **+ 추가**를 클릭합니다. 중단점이 적중되어야 합니다. VS Code 맨 위에 있는 디버그 도구 모음을 사용하여 실행을 계속하거나, 줄을 건너뛰거나, 메서드를 한 단계씩 실행하거나, 현재 메서드에서 나갈 수 있습니다. 
   
   ![중단점 적중](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. 디버깅 세션을 종료하려면 VS Code 맨 위에 있는 디버그 도구 모음에서 플러그 아이콘을 클릭합니다.
   
   ![디버거에서 연결 끊기](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. 디버깅을 완료하면 **Service Fabric: 응용 프로그램 제거** 명령 Voting 응용 프로그램이 로컬 클러스터에서 제거 합니다. 

## <a name="next-steps"></a>다음 단계

* [VS Code를 통해 C# Service Fabric 응용 프로그램 개발 및 디버그](./service-fabric-develop-csharp-applications-with-vs-code.md) 방법을 알아봅니다.
