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
작성 및 제출 합니다 [Cognitive Services 비전 컨테이너 요청 양식](https://aka.ms/VisionContainersPreview) 컨테이너에 액세스를 요청 합니다. 이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 폼을 제출 하면 Azure Cognitive Services 팀은 개인 컨테이너 레지스트리에 대 한 액세스 조건을 충족 하는지을 검토 합니다.

> [!IMPORTANT]
> Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 계정 형식에서 중 하나를 사용 하 여 연결 된 전자 메일 주소를 사용 해야 합니다.

요청이 승인 되 면 자격 증명을 가져오는 개인 컨테이너 레지스트리에 액세스 하는 방법을 설명 하는 지침을 사용 하 여 전자 메일을 수신 합니다.

## <a name="log-in-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 로그인

Cognitive Services 컨테이너에 대 한 개인 컨테이너 레지스트리로 인증 하는 방법은 여러 가지가 있습니다. 사용 하 여 명령줄 메서드를 사용 하는 것이 좋습니다 합니다 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)합니다.

사용 된 [docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 에 로그인 하기 위해 다음 예에 표시 된 대로 명령을 `containerpreview.azurecr.io`, Cognitive Services 컨테이너에 대 한 개인 컨테이너 레지스트리에. *\<username\>* 과 *\<password\>* 를 각각 Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 사용자 이름과 암호로 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호 하는 경우에 해당 텍스트 파일의 내용을 연결할 수 있습니다는 `docker login` 명령입니다. 사용 된 `cat` 명령을 다음 예와에서 같이 합니다. 바꿉니다 *\<passwordFile\>* 암호가 포함 된 텍스트 파일의 이름과 경로 사용 하 여 합니다. 바꿉니다 *\<사용자 이름\>* 의 사용자 이름 자격 증명을 제공 합니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

