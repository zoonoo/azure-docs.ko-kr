---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4f061dc66829c49fba6722215dc36ed2ce6464ee
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919301"
---
Azure Cloud Shell에서 [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 명령을 사용하여 배포 자격 증명을 구성합니다. 이 배포 사용자는 웹앱에 대한 FTP 및 로컬 Git 배포에 필요합니다. 사용자 이름과 암호는 계정 수준이며, _Azure 구독 자격 증명과 다릅니다._

다음 예에서 *\<username>* 및 *\<password>*(대괄호 포함)를 새 사용자 이름 및 암호로 바꿉니다. 사용자 이름은 Azure 내에서 고유해야 합니다. 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

`null`로 표시된 암호와 함께 JSON 출력이 표시됩니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. ` 'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 배포 사용자 이름에 로컬 Git 푸시에 대한 '@' 기호가 포함되면 안 됩니다.

이 배포 사용자는 한 번만 구성하면 됩니다. 모든 Azure 배포에 사용할 수 있습니다.

> [!NOTE]
> 사용자 이름 및 암호를 기록합니다. 기록해 둡니다.
>
>
