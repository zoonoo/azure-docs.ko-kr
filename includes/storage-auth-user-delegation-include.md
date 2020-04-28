---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76118103"
---
## <a name="about-the-user-delegation-sas"></a>사용자 위임 SAS 정보

컨테이너 또는 blob에 액세스 하기 위한 SAS 토큰은 Azure AD 자격 증명 또는 계정 키를 사용 하 여 보호 될 수 있습니다. 사용자를 대신 하 여 SAS에 서명 하는 데 사용 되는 OAuth 2.0 토큰이 요청 되기 때문에 Azure AD 자격 증명으로 보호 된 SAS를 사용자 위임 SAS 라고 합니다.

가능 하면 계정 키를 사용 하는 대신 보안 모범 사례로 Azure AD 자격 증명을 사용 하는 것이 좋습니다 .이는 더 쉽게 손상 될 수 있습니다. 응용 프로그램 디자인에 공유 액세스 서명이 필요한 경우 Azure AD 자격 증명을 사용 하 여 우수한 보안을 위한 사용자 위임 SAS를 만듭니다. 사용자 위임 SAS에 대 한 자세한 내용은 [사용자 위임 Sas 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조 하세요.

> [!CAUTION]
> 유효한 SAS를 소유한 클라이언트는 해당 SAS에서 허용한 대로 저장소 계정의 데이터에 액세스할 수 있습니다. 악의적 이거나 의도 하지 않은 사용 으로부터 SAS를 보호 하는 것이 중요 합니다. SAS 배포에는 신중 하 게를 사용 하 고 손상 된 SAS를 해지 하는 계획을 세워야 합니다.

공유 액세스 서명에 대 한 자세한 내용은 [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../articles/storage/common/storage-sas-overview.md)를 참조 하세요.
