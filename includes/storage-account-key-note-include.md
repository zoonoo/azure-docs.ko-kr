---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460571"
---
## <a name="protect-your-access-keys"></a>액세스 키 보호

저장소 계정 액세스 키는 저장소 계정의 루트 암호와 유사합니다. 항상 액세스 키를 보호하십시오. Azure 키 자격 증명 모음을 사용하여 키를 안전하게 관리하고 회전합니다. 액세스 키를 다른 사용자에게 배포하거나, 하드 코딩하거나, 다른 사용자가 액세스할 수 있는 일반 텍스트의 아무 곳에도 저장하지 마십시오. 키가 손상되었다고 생각되면 키를 회전합니다.

가능하면 Azure Active Directory(Azure AD)를 사용하여 공유 키 대신 Blob 및 Queue 저장소에 대한 요청을 승인합니다. Azure AD는 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하여 데이터에 대한 액세스 권한을 부여하는 자세한 내용은 Azure Active Directory 를 [사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여를](../articles/storage/common/storage-auth-aad.md)참조하세요.
