---
author: baanders
description: Azure Digital Twins용 포함 파일 - Cloud Shell 인증과 관련된 알려진 문제 인용
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92324898"
---
>[!NOTE]
>현재 Cloud Shell에는 *https://shell.azure.com* 에서 실행될 때 `az dt route`, `az dt model`, `az dt twin` 명령 그룹에 영향을 주는 **알려진 문제** 가 있습니다.
>
>해결하려면 다음 중 하나를 수행하면 됩니다.
> * 명령을 실행하기 전에 Cloud Shell에서 `az login`을 실행합니다.
> * Azure Portal에서 Cloud Shell 창을 열고 여기에서 Cloud Shell 작업을 완료합니다.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="'Cloud Shell' 아이콘이 강조 표시되고 포털 창 하단에 Cloud Shell이 표시되는 Azure Portal 보기":::
> * Cloud Shell 대신 [로컬 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용합니다.
>
>이 문제에 대한 자세한 내용은 [*문제 해결: Azure Digital Twins의 알려진 문제*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)를 참조하세요.