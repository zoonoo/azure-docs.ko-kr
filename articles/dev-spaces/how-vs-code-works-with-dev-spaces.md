---
title: Azure Dev Spaces에서 Visual Studio Code가 작동하는 방식
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code 및 Azure Dev Spaces가 Kubernetes 애플리케이션을 디버그하고 신속하게 반복하는 데 어떻게 도움이 되는지 알아봅니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960748"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Azure Dev Spaces에서 Visual Studio Code가 작동하는 방식

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Visual Studio Code 및 [Azure Dev Spaces 확장][azds-extension]을 사용하여 Azure Dev Spaces로 서비스를 준비, 실행, 디버그할 수 있습니다. Visual Studio Code 및 Azure Dev Spaces 확장을 사용하면 다음을 수행할 수 있습니다.

* AKS에서 서비스를 실행하고 디버그하기 위한 자산 생성
* 개발 공간에서 Java, Node.js, .NET Core 서비스를 실행합니다.
* 개발 공간에서 실행되는 Java, Node.js, .NET Core 서비스 직접 디버그

## <a name="generate-assets"></a>자산 생성

Visual Studio Code 및 Azure Dev Spaces 확장은 다음과 같은 프로젝트 자산을 생성합니다.

* Maven을 사용하는 Java 애플리케이션, Node.js 애플리케이션, .NET Core 애플리케이션용 Dockerfile
* Dockerfile을 사용하는 거의 모든 언어용 Helm 차트
* 프로젝트의 [Azure Dev Spaces 구성 파일][azds-yaml]인 `azds.yaml` 파일
* Maven을 사용하는 Java 애플리케이션, Node.js 애플리케이션, .NET Core 애플리케이션용 프로젝트의 Visual Studio Code 시작 구성이 포함된 `.vscode` 폴더

Dockerfile, Helm 차트, `azds.yaml` 파일은 `azds prep`을 실행할 때 생성되는 것과 동일한 자산입니다. Visual Studio Code 외부에서 해당 파일을 사용하여 AKS에서 프로젝트를 실행(예: `azds up` 실행)할 수도 있습니다. `.vscode` 폴더는 Visual Studio Code를 통해 AKS에서 프로젝트를 실행하는 경우에만 Visual Studio Code에서 사용됩니다.

## <a name="run-your-service-in-aks"></a>AKS에서 서비스 실행

프로젝트의 자산을 생성한 후에는 Visual Studio Code를 통해 기존 개발 공간에서 Java, Node.js, .NET Core 서비스를 실행할 수 있습니다. Visual Studio Code의 ‘디버그’ 페이지에서 `.vscode` 디렉터리의 시작 구성을 호출하여 프로젝트를 실행할 수 있습니다.

Visual Studio Code 외부에서 AKS 클러스터를 만들고 클러스터의 Azure Dev Spaces를 사용하도록 설정해야 합니다. `azds prep`을 실행하여 생성된 자산과 같이 Visual Studio Code 외부에서 만든 기존 Dockerfile, Helm 차트, `azds.yaml` 파일을 재사용할 수 있습니다. Visual Studio Code 외부로 생성된 자산을 재사용하는 경우에도 `.vscode` 디렉터리가 있어야 합니다. `.vscode` 디렉터리는 Visual Studio Code 및 Azure Dev Spaces 확장에서 다시 생성할 수 있으며 기존 자산을 덮어쓰지 않습니다.

.NET Core 프로젝트의 경우 Visual Studio Code에서 .NET 서비스를 실행하려면 [C# 확장][csharp-extension]이 설치되어 있어야 합니다. 또한 Maven을 사용하는 Java 프로젝트의 경우 Visual Studio Code에서 Java 서비스를 실행하려면[Azure Dev Spaces 확장용 Java 디버거][java-extension]가 설치되어 있고 [Maven이 설치 및 구성][maven]되어 있어야 합니다.

## <a name="debug-your-service-in-aks"></a>AKS에서 서비스 디버그

프로젝트를 시작한 후에는 개발 공간에서 실행되는 Java, Node.js, .NET Core 서비스를 Visual Studio Code에서 직접 디버그할 수 있습니다. `.vscode` 디렉터리의 시작 구성은 개발 공간에서 디버깅을 사용하도록 설정하여 서비스를 실행하기 위한 추가 디버깅 정보를 제공합니다. 또한 Visual Studio Code는 개발 공간에서 실행 중인 컨테이너의 디버그 프로세스에 연결하므로 중단점을 설정하고, 변수를 검사하고, 다른 디버깅 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방식에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
