---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249939"
---
Azure AD 리소스 ID는 발급된 토큰을 사용하여 Azure 리소스에 대한 액세스를 제공할 수 있는 대상을 나타냅니다. Azure Storage의 경우 리소스 ID는 단일 저장소 계정에만 적용되거나 모든 저장소 계정에 적용될 수 있습니다. 다음 표에서는 리소스 ID에 대해 제공할 수 있는 값을 설명합니다.

|리소스 ID  |설명  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 지정된 저장소 계정의 서비스 끝점입니다. 이 값을 사용하여 특정 Azure Storage 계정 및 서비스에만 요청을 승인하기 위한 토큰을 획득합니다. 괄호 속의 값을 저장소 계정의 이름으로 바꿉니다.      |
|`https://storage.azure.com/`     | Azure Storage 계정에 대한 요청을 승인하기 위한 토큰을 획득하는 데 사용합니다.        |
