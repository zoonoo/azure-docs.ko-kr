---
title: 포함 파일
description: 포함 파일
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91672081"
---
1. 다음과 같이 Azure NetApp Files에 대해 승인된 구독을 지정합니다.
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. 다음과 같이 Azure 리소스 공급자를 등록합니다. 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```