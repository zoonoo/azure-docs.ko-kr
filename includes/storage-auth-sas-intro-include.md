---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011934"
---
SAS (공유 액세스 서명)를 사용 하면 저장소 계정의 컨테이너 및 blob에 대 한 제한 된 액세스 권한을 부여할 수 있습니다. SAS를 만들 때 클라이언트에서 액세스할 수 있는 Azure Storage 리소스, 해당 리소스에 대 한 사용 권한 및 SAS의 유효 기간을 포함 하 여 해당 제약 조건을 지정 합니다.

모든 SAS는 키로 서명 됩니다. 다음 두 가지 방법 중 하나로 SAS에 서명할 수 있습니다.

- Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 만든 키입니다. Azure AD 자격 증명으로 서명 된 SAS는 *사용자 위임* sas입니다.
- 저장소 계정 키를 사용 합니다. *서비스 sas* 와 *계정 sas* 는 모두 저장소 계정 키로 서명 됩니다.
