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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460571"
---
## <a name="protect-your-access-keys"></a>액세스 키 보호

저장소 계정 액세스 키는 저장소 계정의 루트 암호와 비슷합니다. 항상 액세스 키를 보호 해야 합니다. Azure Key Vault를 사용 하 여 키를 안전 하 게 관리 하 고 회전 합니다. 액세스 키를 다른 사용자에 게 배포 하거나 하드 코딩 하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장 하지 않도록 합니다. 키가 손상 된 것으로 판단 되 면 키를 회전 합니다.

가능 하면 Azure AD (Azure Active Directory)를 사용 하 여 공유 키 대신 Blob 및 큐 저장소에 대 한 요청에 권한을 부여 합니다. Azure AD는 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공 합니다. Azure AD에서 데이터에 대 한 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 azure blob 및 큐에](../articles/storage/common/storage-auth-aad.md)대 한 액세스 권한 부여
