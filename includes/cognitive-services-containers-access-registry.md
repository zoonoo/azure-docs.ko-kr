---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704283"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Docker CLI를 사용 하 여 개인 컨테이너 레지스트리 인증

여러 가지 방법으로 Cognitive Services 컨테이너에 대해 개인 컨테이너 레지스트리를 사용 하 여 인증할 수 있지만 명령줄에서 권장 되는 방법은 [DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/)를 사용 하는 것입니다.

다음 예제와 같이 [ `docker login` 명령을](https://docs.docker.com/engine/reference/commandline/login/)사용 하 여 `containerpreview.azurecr.io` Cognitive Services 컨테이너에 대 한 개인 컨테이너 레지스트리로 로그인 합니다. 을 *\<username\>* 사용자 이름으로 바꾸고,을 *\<password\>* Azure Cognitive Services 팀에서 받은 자격 증명에 제공 된 암호로 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호 하는 경우 `cat` 다음 예제와 같이 명령을 사용 하 여 명령을 사용 하 여 해당 텍스트 파일의 내용을 연결할 수 있습니다 `docker login` . 을 *\<passwordFile\>* *\<username\>* 자격 증명에 제공 된 사용자 이름 및 암호를 포함 하는 텍스트 파일의 경로 및 이름으로 바꿉니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
