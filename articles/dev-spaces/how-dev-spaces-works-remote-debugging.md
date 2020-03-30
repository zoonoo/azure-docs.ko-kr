---
title: Azure 개발자 공간에서 코드를 원격 디버깅하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure 개발자 공간을 사용하여 Azure Kubernetes 서비스에서 원격 디버깅 프로세스에 대해 설명합니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241401"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Azure 개발자 공간에서 코드를 원격 디버깅하는 방법

Azure 개발자 공간은 Kubernetes 응용 프로그램을 빠르게 반복 및 디버깅하고 AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업하는 여러 가지 방법을 제공합니다. 프로젝트가 개발 공간에서 실행되면 Azure 개발자 공간은 AKS에서 실행 중인 응용 프로그램에 연결하고 디버깅하는 방법을 제공합니다.

이 문서에서는 Dev Spaces를 사용하여 원격 디버깅이 작동하는 방식을 설명합니다.

## <a name="debug-your-code"></a>코드 디버그

Java, .NET Core 및 Node.js 응용 프로그램의 경우 Visual Studio 코드 또는 Visual Studio를 사용하여 개발 공간에서 직접 실행되는 응용 프로그램을 디버깅할 수 있습니다. Visual Studio 코드 및 Visual Studio는 개발 공간에 연결하고 응용 프로그램을 실행하고 디버거를 연결하는 도구를 제공합니다. 실행한 `azds prep`후 Visual Studio 코드 또는 Visual Studio에서 프로젝트를 열 수 있습니다. Visual Studio 코드 또는 Visual Studio는 실행과 `azds prep`는 별개의 연결에 대한 자체 구성 파일을 생성합니다. Visual Studio 코드 또는 Visual Studio 내에서 중단점을 설정하고 개발 공간으로 응용 프로그램을 시작할 수 있습니다.

![코드 디버그](media/get-started-node/debug-configuration-nodejs2.png)

디버깅을 위해 Visual Studio Code 또는 Visual Studio를 사용하여 응용 프로그램을 시작할 때 실행과 `azds up`동일한 방식으로 개발 공간에 대한 시작 및 연결처리를 처리합니다. 또한 Visual Studio 코드 및 Visual Studio의 클라이언트 쪽 툴링은 각각 디버깅에 대한 특정 정보가 있는 추가 매개 변수를 제공합니다. 매개 변수에는 디버거 이미지 이름, 디버거 이미지 내의 디버거 위치 및 디버거 폴더를 탑재할 응용 프로그램의 컨테이너 내의 대상 위치가 포함됩니다.

디버거 이미지는 클라이언트 측 툴링에 의해 자동으로 결정됩니다. 을 실행할 `azds prep`때 Dockerfile 및 Helm 차트 생성 중에 사용되는 것과 유사한 메서드를 사용합니다. 디버거가 응용 프로그램의 이미지에 탑재되면 을 사용하여 `azds exec`실행됩니다.

## <a name="next-steps"></a>다음 단계

프로젝트를 원격으로 디버깅하기 위해 Azure 개발자 공간을 사용하여 시작하려면 다음 빠른 시작을 참조하십시오.

* [Visual Studio 코드 및 Java를 통해 빠르게 반복 및 디버깅][quickstart-java]
* [Visual Studio 코드 및 .NET을 통해 빠르게 반복 및 디버깅][quickstart-netcore]
* [Visual Studio 코드 및 Node.js로 빠르게 반복 및 디버깅][quickstart-node]
* [Visual Studio 및 .NET Core를 통해 빠르게 반복 및 디버깅][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
