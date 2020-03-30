---
title: Azure 개발자 공간에서 Visual Studio 코드의 작동 방식
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: 비주얼 스튜디오 코드 및 Azure 개발자 공간이 Kubernetes 응용 프로그램을 디버깅하고 빠르게 반복하는 데 어떻게 도움이 되는지 알아보십시오.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240454"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Azure 개발자 공간에서 Visual Studio 코드의 작동 방식

Visual Studio 코드 및 [Azure 개발자 공간 확장을][azds-extension] 사용하여 Azure 개발자 공간을 사용하여 서비스를 준비, 실행 및 디버깅할 수 있습니다. 비주얼 스튜디오 코드 및 Azure 개발자 공간 확장을 사용하면 다음을 수행할 수 있습니다.

* AKS에서 서비스 실행 및 디버깅을 위한 자산 생성
* 개발 공간에서 Java, Node.js 및 .NET Core 서비스를 실행합니다.
* 개발 공간에서 실행되는 Java, Node.js 및 .NET Core 서비스를 직접 디버깅합니다.

## <a name="generate-assets"></a>자산 생성

비주얼 스튜디오 코드 및 Azure 개발자 공간 확장은 프로젝트에 대해 다음과 같은 자산을 생성합니다.

* Maven, Node.js 응용 프로그램 및 .NET 코어 응용 프로그램을 사용하는 Java 응용 프로그램에 대한 Dockerfiles
* Dockerfile을 사용 하 여 거의 모든 언어에 대 한 헬름 차트
* 프로젝트에 `azds.yaml` 대한 Azure [개발자 공간 구성 파일인][azds-yaml] 파일
* Maven, Node.js 응용 프로그램 및 .NET Core 응용 프로그램을 사용하는 Java 응용 프로그램에 대한 프로젝트의 Visual Studio 코드 시작 구성이 있는 `.vscode` 폴더

Dockerfile, 헬름 차트 `azds.yaml` 및 파일은 을 `azds prep`실행할 때 생성된 것과 동일한 자산입니다. 이러한 파일은 Visual Studio 코드 외부에서 사용하여 실행과 `azds up`같은 AKS에서 프로젝트를 실행할 수도 있습니다. 폴더는 `.vscode` Visual Studio 코드에서 AKS에서 프로젝트를 실행하는 데만 사용됩니다.

## <a name="run-your-service-in-aks"></a>AKS에서 서비스 실행

프로젝트에 대한 자산을 생성한 후 Visual Studio Code의 기존 개발 공간에서 Java, Node.js 및 .NET Core 서비스를 실행할 수 있습니다. Visual Studio 코드의 *디버그* 페이지에서 디렉터리에서 시작 구성을 `.vscode` 호출하여 프로젝트를 실행할 수 있습니다.

AKS 클러스터를 만들고 Visual Studio 코드 외부의 클러스터에서 Azure 개발자 공간을 사용하도록 설정해야 합니다. 예를 들어 Azure CLI 또는 Azure 포털을 사용하여 이 설정을 수행할 수 있습니다. 을 실행하여 `azds prep`생성된 자산과 같이 `azds.yaml` Visual Studio 코드 외부에서 생성된 기존 Dockerfiles, 헬름 차트 및 파일을 다시 사용할 수 있습니다. Visual Studio 코드 외부에서 생성된 에셋을 재사용하는 경우에도 `.vscode` 디렉터리가 있어야 합니다. 이 `.vscode` 디렉터리Visual Studio 코드 및 Azure 개발자 공간 확장에 의해 다시 생성될 수 있으며 기존 자산을 덮어쓰지 않습니다.

.NET Core 프로젝트의 경우 Visual Studio 코드에서 .NET 서비스를 실행하려면 [C# 확장이][csharp-extension] 설치되어 있어야 합니다. 또한 Maven을 사용하는 Java 프로젝트의 경우 [Azure 개발자 공간 확장에 대한 Java 디버거가][java-extension] 설치되어 있어야 하며 Visual Studio 코드에서 Java 서비스를 실행하도록 [Maven이 설치및 구성되어야][maven] 합니다.

## <a name="debug-your-service-in-aks"></a>AKS에서 서비스 디버깅

프로젝트를 실행한 후 Visual Studio 코드에서 직접 개발 공간에서 실행되는 Java, Node.js 및 .NET Core 서비스를 디버깅할 수 있습니다. `.vscode` 디렉터리에서 시작 구성은 개발 공간에서 활성화된 디버깅을 통해 서비스를 실행하기 위한 추가 디버깅 정보를 제공합니다. 또한 Visual Studio Code는 개발 공간의 실행 중인 컨테이너에서 디버그 프로세스에 연결되므로 중단점을 설정하고 변수를 검사하며 다른 디버깅 작업을 수행할 수 있습니다.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Azure 개발자 공간에서 시각적 스튜디오 코드 사용

다음과 같은 빠른 시작에서 Azure 개발자 공간에서 작업하는 시각적 스튜디오 코드 및 Azure 개발자 공간 확장 프로그램을 볼 수 있습니다.

* [Visual Studio 코드 및 Java를 통해 빠르게 반복 및 디버깅][quickstart-java]
* [Visual Studio 코드 및 .NET을 통해 빠르게 반복 및 디버깅][quickstart-netcore]
* [Visual Studio 코드 및 Node.js로 빠르게 반복 및 디버깅][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
