---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 08e6b5d109d6647f2a291f117f4993bae7598464
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302326"
---
컨테이너 액세스 권한을 요청하려면 먼저 [Cognitive Services Vision 컨테이너 요청 양식](https://aka.ms/VisionContainersPreview)을 완성하여 제출해야 합니다. 이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 제출되면 Azure Cognitive Services 팀에서 양식을 검토하여 개인 컨테이너 레지스트리에 대한 액세스 조건을 충족하는지 확인합니다.

> [!IMPORTANT]
> 양식의 MSA(Microsoft 계정) 또는 Azure AD(Azure Active Directory) 계정과 연결된 이메일 주소를 사용해야 합니다.

요청이 승인되면 자격 증명을 얻고 개인 컨테이너 레지스트리에 액세스하는 방법을 설명하는 지침이 포함된 이메일을 받게 됩니다.

## <a name="log-in-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 로그인

Cognitive Services 컨테이너용 개인 컨테이너 레지스트리를 사용하여 인증하는 몇 가지 방법이 있지만, 명령줄에서 권장되는 방법은 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)를 사용하는 것입니다.

다음 예제와 같이 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Cognitive Services 컨테이너용 개인 컨테이너 레지스트리인 `containerpreview.azurecr.io`에 로그인합니다. *\<username\>* 과 *\<password\>* 를 각각 Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 사용자 이름과 암호로 바꿉니다.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호한 경우 다음 예제와 같이 `cat` 명령을 사용하여 해당 텍스트 파일의 내용을 `docker login` 명령에 연결할 수 있습니다. *\<passwordFile\>* 을 암호가 포함된 텍스트 파일의 경로와 이름으로, *\<username\>* 을 사용자 자격 증명에 제공된 사용자 이름으로 바꿉니다.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

