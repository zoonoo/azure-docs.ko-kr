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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118103"
---
## <a name="about-the-user-delegation-sas"></a>사용자 위임 SAS 정보

컨테이너 또는 Blob에 대한 액세스를 위한 SAS 토큰은 Azure AD 자격 증명 또는 계정 키를 사용하여 보호될 수 있습니다. Azure AD 자격 증명으로 보안된 SAS를 사용자 위임 SAS라고 하는데, 이는 사용자를 대신하여 SAS에 서명하는 데 사용되는 OAuth 2.0 토큰이 요청되기 때문입니다.

더 쉽게 손상될 수 있는 계정 키를 사용하는 대신 가능한 경우 Azure AD 자격 증명을 보안 모범 사례로 사용하는 것이 좋습니다. 응용 프로그램 디자인에 공유 액세스 서명이 필요한 경우 Azure AD 자격 증명을 사용하여 뛰어난 보안을 위해 사용자 위임 SAS를 만듭니다. 사용자 위임 SAS에 대한 자세한 내용은 [사용자 위임 SAS 만들기를](/rest/api/storageservices/create-user-delegation-sas)참조하십시오.

> [!CAUTION]
> 유효한 SAS를 소유한 모든 클라이언트는 해당 SAS에서 허용하는 대로 저장소 계정의 데이터에 액세스할 수 있습니다. SAS를 악의적이거나 의도하지 않은 사용으로부터 보호하는 것이 중요합니다. SAS배포에 재량권을 사용하고 손상된 SAS를 취소할 계획을 수립합니다.

공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여를](../articles/storage/common/storage-sas-overview.md)참조하십시오.
