---
title: Visual Studio Code Azure 개발 공간에서 작동 하는 방법
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code Azure 개발 공간에서 작동 하는 방법
keywords: Azure 개발 공간, 개발 공간, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712151"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code Azure 개발 공간에서 작동 하는 방법

Visual Studio Code를 사용할 수 있습니다 및 [Azure 개발 공간 확장][azds-extension] 준비, 실행 및 Azure 개발 공백을 사용 하 여 서비스를 디버그 합니다. Visual Studio Code 및 Azure 개발 공간 확장을 사용 하 여 다음을 수행할 수 있습니다.

* 실행 및 디버깅 AKS에서 서비스에 대 한 자산을 생성 합니다.
* 개발 공간에서 Java, Node.js 및.NET Core 서비스를 실행 합니다.
* 직접 개발 공간에서 실행 중인 Java, Node.js 및.NET Core 서비스 디버그

## <a name="generate-assets"></a>자산 생성

Visual Studio Code 및 Azure 개발 공간 확장 프로젝트에 대 한 다음 자산을 생성합니다.

* Maven을 사용 하 여 Java 응용 프로그램에서 Node.js 응용 프로그램 및.NET Core 응용 프로그램에 대 한 Dockerfile
* Dockerfile 사용 하 여 거의 모든 언어에 대 한 helm 차트
* `azds.yaml` 파일을 [Azure 개발 공간 구성 파일][azds-yaml] 프로젝트
* `.vscode` Maven을 사용 하 여 Java 응용 프로그램에서 Node.js 응용 프로그램 및.NET Core 응용 프로그램에 대 한 프로젝트의 Visual Studio Code 시작 구성 사용 하 여 폴더

Dockerfile, Helm 차트 및 `azds.yaml` 파일을 실행할 때 생성 된 동일한 자산 `azds prep`합니다. 해당 파일 또한 외부에서 사용할 수 Visual Studio 코드 실행과 같은 AKS에서 프로젝트를 실행 하려면 `azds up`합니다. `.vscode` AKS에서 Visual Studio Code에서 프로젝트를 실행 하려면 Visual Studio code 폴더는만 사용 합니다.

## <a name="run-your-service-in-aks"></a>AKS에서 서비스를 실행 합니다.

프로젝트에 대 한 자산을 생성 한 후에 Visual Studio Code에서 기존 개발 공간에서 Java, Node.js 및.NET Core 서비스를 실행할 수 있습니다. 에 *디버그* 페이지의 Visual Studio Code 시작 구성을 호출할 수 있습니다는 `.vscode` 프로젝트를 실행 하는 디렉터리입니다.

AKS 클러스터를 만들고 Visual Studio Code 외부에서 클러스터의 Azure 개발 공간을 사용 하도록 설정 해야 합니다. 예를 들어,이 설치를 위해 Azure CLI 또는 Azure portal을 사용할 수 있습니다. 기존 Dockerfile, Helm 차트를 다시 사용할 수 있습니다 하 고 `azds.yaml` 실행 하 여 생성 한 자산 등 Visual Studio Code 외부에서 생성 된 파일 `azds prep`합니다. Visual Studio Code 외부에서 생성 된 자산을 다시 사용 수행 해야 할를 `.vscode` 디렉터리입니다. 이 `.vscode` 디렉터리 Visual Studio code 및 Azure 개발 공간 확장 하 여 다시 생성할 수 있습니다 하 고 기존 자산을 덮어쓰지 것입니다.

.NET Core 프로젝트에 있어야 합니다 [ C# 확장][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] 와 설치 된 [Maven 설치 및 구성][maven] Java 프로그램을 실행 하려면 Visual Studio Code에서 서비스입니다.

## <a name="debug-your-service-in-aks"></a>AKS에서 서비스를 디버그

프로젝트를 시작한 후에 Visual Studio Code에서 직접 개발 공간에서 실행 중인 Java, Node.js 및.NET Core 서비스를 디버깅할 수 있습니다. 시작 구성에는 `.vscode` 디렉터리 디버깅 개발 공간에서 사용 된 서비스를 실행 하는 것에 대 한 추가 디버깅 정보를 제공 합니다. Visual Studio Code도 프로세스에 연결 디버그에 개발 공간에서 실행 중인 컨테이너에 중단점을 설정, 변수를 검사 및 기타 디버깅 작업을 수행할 수 있습니다.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Azure 개발 공백을 사용 하 여 Visual Studio Code 사용

Visual Studio Code 및 Azure 개발 공간 확장 다음 빠른 시작에서 Azure 개발 공백을 사용 하 여 작업을 확인할 수 있습니다.

* [Java를 사용 하 여 개발][quickstart-java]
* [.NET을 사용 하 여 개발][quickstart-netcore]
* [Node.js를 사용 하 여 개발][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md