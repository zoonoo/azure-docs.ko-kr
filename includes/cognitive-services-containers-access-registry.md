---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125154"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Docker CLI를 사용하여 프라이빗 컨테이너 레지스트리 인증

Cognitive Services 컨테이너용 프라이빗 컨테이너 레지스트리를 사용하여 인증하는 몇 가지 방법이 있지만, 명령줄에서 권장되는 방법은 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)를 사용하는 것입니다.

다음 예제와 같이 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Cognitive Services 컨테이너용 프라이빗 컨테이너 레지스트리인 `containerpreview.azurecr.io`에 로그인합니다. *\<username\>* 과 *\<password\>* 를 각각 Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 사용자 이름과 암호로 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호한 경우 다음 예제와 같이 `cat` 명령을 사용하여 해당 텍스트 파일의 내용을 `docker login` 명령에 연결할 수 있습니다. *\<passwordFile\>* 을 암호가 포함된 텍스트 파일의 경로와 이름으로, *\<username\>* 을 사용자 자격 증명에 제공된 사용자 이름으로 바꿉니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
