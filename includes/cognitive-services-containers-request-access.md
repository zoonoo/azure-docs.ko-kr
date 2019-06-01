---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124577"
---
컨테이너에 액세스를 요청하려면 [Cognitive Services 비전 컨테이너 요청 양식](https://aka.ms/VisionContainersPreview)을 작성 및 제출 합니다. 이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식을 제출 하면 Azure Cognitive Services 팀은 개인 컨테이너 레지스트리에 대한 액세스 조건을 충족 하는지을 검토 합니다.

> [!IMPORTANT]
> Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 계정 형식에서 중 하나를 사용 하 여 연결된 전자 메일 주소를 사용 해야 합니다.

요청이 승인되면 자격 증명을 얻고 개인 컨테이너 레지스트리에 액세스 하는 방법을 설명 하는 지침이 있는 전자 메일을 수신 합니다.

## <a name="log-in-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 로그인

Cognitive Services 컨테이너에 대한 개인 컨테이너 레지스트리로 인증 하는 방법은 여러 가지가 있습니다. [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)를 사용하는 명령줄 메서드를 권장 합니다.

다음 예에 표시된 대로 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Cognitive Services 컨테이너에 대한 개인 컨테이너 레지스트리 `containerpreview.azurecr.io`에 로그인합니다. *\<username\>* 과 *\<password\>* 를 각각 Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 사용자 이름과 암호로 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호 하는 경우, 해당 텍스트 파일의 내용을 `docker login` 명령으로 연결할 수 있습니다. 다음 예와 같이 `cat` 명령을 사용합니다. *\<passwordFile\>* 를 암호가 포함된 텍스트 파일의 이름과 경로로 바꿉니다. *\<사용자 이름\>* 을 자격 증명에서 제공된 사용자 이름으로 바꿉니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

