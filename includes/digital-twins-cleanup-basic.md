---
author: baanders
description: Azure Digital Twins 인스턴스를 정리하기 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0783f25813fa8e4a65a33975017e2b2d03a4dd2c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110075495"
---
* **이 자습서에서 만든 리소스가 필요하지 않은 경우** [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 명령을 사용하여 이 문서에서 Azure Digital Twins 인스턴스와 기타 모든 리소스를 삭제할 수 있습니다. 이는 리소스 그룹의 모든 Azure 리소스와 리소스 그룹 자체를 삭제합니다.
    
    > [!IMPORTANT]
    > 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다.
    
    [Azure Cloud Shell](https://shell.azure.com)을 열고 다음 명령을 실행하여 리소스 그룹과 그 안에 포함된 모든 항목을 삭제합니다.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```