---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 07/23/2021
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: d754af9217353ca2f782c44f95aa488ede7a555f
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688814"
---
다운로드한 프로젝트에서 appsettings.json 파일을 엽니다. 값을 [API 액세스](../access-api-howto.md)에서 가져온 자격 증명으로 바꿉니다.

> [!NOTE]
> 또한 프로젝트 루트에서 *.env* 파일 형식을 사용하여 .NET 샘플 리포지토리의 모든 프로젝트에 대해 환경 변수를 한 번만 설정할 수 있습니다. *sample.env* 파일을 복사한 다음, Azure Portal의 Media Services **API 액세스** 페이지 또는 Azure CLI에서 가져온 정보를 입력하기만 하면 됩니다. 모든 프로젝트에서 사용하려면 *sample.env* 파일의 이름을 *.env* 로 바꿉니다.<br/><br/>
> *.gitignore* 파일은 포크 처리된 리포지토리에 이 파일이 게시되지 않도록 이미 구성되어 있습니다.
