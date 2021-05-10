---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76118103"
---
## <a name="about-the-user-delegation-sas"></a>사용자 위임 SAS 정보

컨테이너 또는 Blob에 액세스하기 위한 SAS 토큰은 Azure AD 자격 증명 또는 계정 키를 사용하여 보호할 수 있습니다. 사용자를 대신하여 SAS에 서명하는 데 사용되는 OAuth 2.0 토큰이 요청되기 때문에 Azure AD 자격 증명의 보호를 받는 SAS를 사용자 위임 SAS라고 합니다.

Microsoft는 더 쉽게 손상될 수 있는 계정 키 대신에 가능하면 Azure AD 자격 증명을 사용하는 것을 보안 모범 사례로 추천합니다. 애플리케이션 설계에 공유 액세스 서명이 필요한 경우, 보안을 강화하기 위해 Azure AD 자격 증명을 사용하여 사용자 위임 SAS를 만듭니다. 사용자 위임 SAS에 대한 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조하세요.

> [!CAUTION]
> 유효한 SAS를 소유한 클라이언트는 해당 SAS에서 허용한 스토리지 계정의 데이터에 액세스할 수 있습니다. 악의적이거나 의도하지 않은 사용으로부터 SAS를 보호하는 것이 중요합니다. SAS 배포는 신중해야 하고, 손상된 SAS를 철회하는 계획을 세워야 합니다.

공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../articles/storage/common/storage-sas-overview.md)를 참조하세요.
