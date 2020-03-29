---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229240"
---
[코그너티브 서비스 비전 컨테이너 요청 양식을](https://aka.ms/VisionContainersPreview) 작성하여 제출하여 컨테이너에 대한 액세스를 요청합니다. 이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식을 제출한 후 Azure Cognitive Services 팀은 양식을 검토하여 개인 컨테이너 레지스트리에 대한 액세스 기준을 충족하는지 확인합니다.

> [!IMPORTANT]
> 양식에서 Microsoft 계정(MSA) 또는 Azure Active Directory(Azure AD) 계정과 연결된 전자 메일 주소를 사용해야 합니다.

요청이 승인되면 자격 증명을 가져오고 개인 컨테이너 레지스트리에 액세스하는 방법을 설명하는 지침이 포함된 전자 메일을 받게 됩니다.

## <a name="log-in-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 로그인

코그너티브 서비스 컨테이너에 대한 개인 컨테이너 레지스트리를 사용하여 인증하는 방법에는 여러 가지가 있습니다. [Docker CLI를](https://docs.docker.com/engine/reference/commandline/cli/)사용하여 명령줄 메서드를 사용하는 것이 좋습니다.

다음 예제와 같이 [docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Cognitive Services `containerpreview.azurecr.io`컨테이너에 대한 개인 컨테이너 레지스트리인 에 로그인합니다. * \<사용자 이름을\> * Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 암호로 사용자 이름 및 * \<암호로\> * 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 확보한 경우 해당 텍스트 파일의 내용을 `docker login` 명령에 연결할 수 있습니다. 다음 `cat` 예제와 같이 명령을 사용합니다. * \<passwordFileFile을\> * 암호가 포함된 텍스트 파일의 경로 및 이름으로 바꿉니다. * \<사용자 이름을\> * 자격 증명에 제공된 사용자 이름으로 바꿉니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

