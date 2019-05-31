---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 04/25/2019
ms.author: kgremban
ms.openlocfilehash: 485a76cb91e5146f59e6be592ffd9cbba68e585a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146691"
---
## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

이 자습서에서는 Azure IoT Tools 확장을 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.

임의 Docker 호환 레지스트리를 사용하여 컨테이너 이미지를 유지할 수 있습니다. 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다.

컨테이너 레지스트리가 아직 없는 경우 다음 단계를 따라 Azure에서 새로 만드세요.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Container Registry**를 선택합니다.

2. 다음 값을 입력하여 컨테이너 레지스트리를 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 레지스트리 이름 | 고유한 이름을 입력합니다. |
   | 구독 | 드롭다운 목록에서 구독을 선택합니다. |
   | 리소스 그룹 | IoT Edge 빠른 시작 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용하는 것이 좋습니다. 예를 들어 **IoTEdgeResources**를 사용합니다. |
   | 위치 | 가까운 위치를 선택합니다. |
   | 관리 사용자 | **사용**으로 설정합니다. |
   | SKU | **기본**을 선택합니다. |

5. **만들기**를 선택합니다.

6. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리를 찾은 다음, **액세스 키**를 선택합니다.

7. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사하고 편리한 위치에 저장합니다. 이 자습서 전반에서 이러한 값을 사용하여 컨테이너 레지스트리에 대한 액세스를 제공합니다.

   ![컨테이너 레지스트리의 로그인 서버, 사용자 이름 및 암호를 복사합니다.](./media/iot-edge-create-container-registry/registry-access-key.png)