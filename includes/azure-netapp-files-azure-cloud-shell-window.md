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
ms.openlocfilehash: 3a63fd96b09910b0cd7ee8c3ab9947b034173c8f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836302"
---
1. 다음과 같이 Azure NetApp Files의 허용 목록에 포함된 구독을 지정합니다.
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. 다음과 같이 Azure 리소스 공급자를 등록합니다. 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```