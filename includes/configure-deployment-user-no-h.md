---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836895"
---
FTP 및 로컬 Git는 *배포 사용자*를 통해 Azure 웹앱에 배포할 수 있습니다. 일단 배포 사용자를 구성하면 모든 Azure 배포에 사용할 수 있습니다. 계정 수준 배포 사용자 이름 및 암호는 Azure 구독 자격 증명과 다릅니다. 

배포 사용자를 구성하려면 Azure Cloud Shell에서 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 명령을 실행합니다. \<username> 및 \<password>를 배포 사용자 이름 및 암호로 바꿉니다. 

- 사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ' @' 기호를 포함하면 안 됩니다. 
- 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 출력에는 암호가 `null`로 나옵니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 

웹앱을 배포할 때 사용할 수 있도록 사용자 이름 및 암호를 기록해 둡니다.
