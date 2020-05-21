---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698541"
---
이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식을 제출한 후 Azure Cognitive Services 팀은이를 검토 하 여 개인 컨테이너 레지스트리에 대 한 액세스 조건을 충족 하는지 확인 합니다.

> [!IMPORTANT]
> 양식의 Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 계정에 연결 된 전자 메일 주소를 사용 해야 합니다.

요청이 승인 되 면 자격 증명을 가져오고 개인 컨테이너 레지스트리에 액세스 하는 방법을 설명 하는 지침이 포함 된 전자 메일을 받게 됩니다.

## <a name="log-in-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 로그인

Cognitive Services 컨테이너에 대해 개인 컨테이너 레지스트리를 사용 하 여 인증 하는 방법에는 여러 가지가 있습니다. [DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/)를 사용 하 여 명령줄 메서드를 사용 하는 것이 좋습니다.

다음 예제와 같이 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용 하 여 `containerpreview.azurecr.io` Cognitive Services 컨테이너의 개인 컨테이너 레지스트리 인에 로그인 합니다. * \< Username \> * 을 Azure Cognitive Services 팀에서 받은 자격 증명에 제공 된 암호와 함께 사용자 이름 및 * \< 암호로 \> * 바꿉니다.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호 한 경우 해당 텍스트 파일의 내용을 명령에 연결할 수 있습니다 `docker login` . `cat`다음 예제와 같이 명령을 사용 합니다. * \< Passwordfile \> * 을 암호를 포함 하는 텍스트 파일의 경로 및 이름으로 바꿉니다. * \< Username \> * 을 자격 증명에 제공 된 사용자 이름으로 바꿉니다.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

