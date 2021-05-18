---
title: Azure Dev Spaces를 사용한 코드 원격 디버깅 소개
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces를 사용한 Azure Kubernetes Service의 원격 디버깅 프로세스에 대해 설명합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975045"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces를 사용한 코드 원격 디버깅 소개

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. 프로젝트가 개발 공간에서 실행되고 있으면 Azure Dev Spaces는 AKS에서 실행 중인 애플리케이션에 연결하고 디버그하는 방법을 제공합니다.

이 문서에서는 Dev Spaces를 사용한 원격 디버깅이 어떻게 작동하는지를 설명합니다.

## <a name="debug-your-code"></a>코드 디버그

Java, .NET Core, Node.js 애플리케이션의 경우 Visual Studio Code 또는 Visual Studio를 사용하여 개발 공간에서 직접 실행 중인 애플리케이션을 디버그할 수 있습니다. Visual Studio Code 및 Visual Studio는 개발 공간에 연결하고, 애플리케이션을 시작하고, 디버거를 연결하는 도구를 제공합니다. `azds prep`을 실행한 후 Visual Studio Code 또는 Visual Studio에서 프로젝트를 열 수 있습니다. Visual Studio Code 또는 Visual Studio는 `azds prep` 실행과 분리된 연결을 위해 자체 구성 파일을 생성합니다. Visual Studio Code 또는 Visual Studio 내에서 중단점을 설정하고 애플리케이션을 시작하여 개발 공간에 연결할 수 있습니다.

![코드 디버그](media/get-started-node/debug-configuration-nodejs2.png)

디버깅을 위해 Visual Studio Code 또는 Visual Studio를 사용하여 애플리케이션을 시작하는 경우 `azds up`을 실행하는 것과 동일한 방식으로 애플리케이션이 시작되고 개발 공간에 연결됩니다. 또한 Visual Studio Code 및 Visual Studio의 클라이언트 쪽 도구는 각각 디버깅을 위한 특정 정보가 포함된 추가 매개 변수를 제공합니다. 매개 변수에는 디버거 이미지 이름, 디버거 이미지 내 디버거 위치, 디버거 폴더를 탑재할 애플리케이션 컨테이너 내 대상 위치가 포함됩니다.

디버거 이미지는 클라이언트 쪽 도구에서 자동으로 결정됩니다. `azds prep`을 실행할 때 Dockerfile 및 Helm 차트 생성 중에 사용된 것과 유사한 메서드가 사용됩니다. 디버거는 애플리케이션 이미지에 탑재된 후 `azds exec`를 사용하여 실행됩니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방식에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)
