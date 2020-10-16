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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68249939"
---
Azure AD 리소스 ID는 발급된 토큰을 사용하여 Azure 리소스에 대한 액세스를 제공할 수 있는 대상을 나타냅니다. Azure Storage의 경우 리소스 ID는 단일 스토리지 계정에 특정되거나 모든 스토리지 계정에 적용될 수 있습니다. 다음 표에서는 리소스 ID에 제공할 수 있는 값을 설명합니다.

|리소스 ID  |설명  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 지정된 스토리지 계정에 대한 서비스 엔드포인트입니다. 이 값을 사용하여 특정 Azure Storage 계정 및 서비스에 대한 요청에 대해서만 권한을 부여하는 토큰을 가져옵니다. 대괄호 안의 값을 스토리지 계정 이름으로 바꿉니다.      |
|`https://storage.azure.com/`     | Azure Storage 계정에 대한 요청을 승인하는 토큰을 가져오는 데 사용합니다.        |
