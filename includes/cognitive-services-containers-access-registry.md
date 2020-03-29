---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704283"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Docker CLI를 사용하여 개인 컨테이너 레지스트리 인증

여러 가지 방법으로 코그너티브 서비스 컨테이너에 대한 개인 컨테이너 레지스트리를 사용하여 인증할 수 있지만 명령줄에서 권장되는 방법은 [Docker CLI를](https://docs.docker.com/engine/reference/commandline/cli/)사용하는 것입니다.

다음 예제와 같이 [ `docker login` 명령을](https://docs.docker.com/engine/reference/commandline/login/)사용하여 Cognitive Services `containerpreview.azurecr.io`컨테이너에 대한 개인 컨테이너 레지스트리에 로그인합니다. * \<사용자 이름을\> * Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 암호로 사용자 이름 및 * \<암호로\> * 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호한 경우 다음 예제와 같이 `cat` 명령을 사용하여 해당 텍스트 파일의 `docker login` 내용을 명령에 연결할 수 있습니다. * \<\> passwordFileFile을* 암호와 * \<\> 사용자 이름이* 포함된 텍스트 파일의 경로 및 이름으로 자격 증명에 제공된 사용자 이름으로 바꿉니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
