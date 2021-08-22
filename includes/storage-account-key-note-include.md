---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef0ad097f0dff99de71096cb24eba511a21bf06a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122264512"
---
## <a name="protect-your-access-keys"></a>액세스 키 보호

스토리지 계정 액세스 키는 스토리지 계정의 루트 암호와 비슷합니다. 항상 액세스 키를 보호해야 합니다. Azure Key Vault를 사용하여 키를 안전하게 관리하고 회전합니다. 액세스 키를 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장하지 않도록 합니다. 키가 손상되었다고 생각되면 키를 교체하세요.

> [!NOTE]
> Microsoft는 가능하면 공유 키 대신 Azure AD(Azure Active Directory)를 사용하여 Blob 및 큐 데이터에 대한 요청에 권한을 부여하는 것이 좋습니다. Azure AD는 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하여 데이터에 대한 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](../articles/storage/blobs/authorize-access-azure-active-directory.md)를 참조하세요.