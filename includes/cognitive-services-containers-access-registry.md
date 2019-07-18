---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704283"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Docker CLI를 사용 하 여 개인 컨테이너 레지스트리를 인증 합니다.

여러 가지 방법 중 하나에 Cognitive 서비스 컨테이너에 대 한 개인 컨테이너 레지스트리를 사용 하 여 인증할 수 있습니다 하지만 명령줄에서 권장 되는 메서드를 사용 하는 것은 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)합니다.

사용 합니다 [ `docker login` 명령](https://docs.docker.com/engine/reference/commandline/login/)에 로그인 하기 위해 다음 예제에서 보여준 것 처럼 `containerpreview.azurecr.io`, Cognitive 서비스 컨테이너에 대 한 개인 컨테이너 레지스트리. 바꿉니다 *\<사용자 이름\>* 사용자 이름 및 *\<암호\>* 에서 받은 자격 증명에 제공 된 암호를 사용 하 여는 Azure Cognitive Services 팀입니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

사용 하 여 해당 텍스트 파일의 내용을 텍스트 파일에서 자격 증명을 보호 한 경우 연결할 수 있습니다 합니다 `cat` 명령이 `docker login` 명령을 다음 예와에서 같이 합니다. 바꿉니다 *\<passwordFile\>* 암호를 포함 하는 텍스트 파일의 이름과 경로 사용 하 여 및 *\<username\>* 사용자 이름 가 제공 하는 자격 증명입니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
