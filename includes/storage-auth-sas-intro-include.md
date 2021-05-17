---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75371782"
---
SAS(공유 액세스 서명)를 사용하면 스토리지 계정의 컨테이너 및 Blob에 대한 제한된 액세스 권한을 부여할 수 있습니다. SAS를 만들 때 클라이언트에서 액세스할 수 있는 Azure Storage 리소스, 해당 리소스에 대한 사용 권한, SAS의 유효 기간 등의 해당 제약 조건을 지정합니다.

모든 SAS는 키로 서명됩니다. 다음 두 가지 방법 중 하나로 SAS에 서명할 수 있습니다.

- Azure AD(Azure Active Directory) 자격 증명을 사용하여 만든 키를 사용합니다. Azure AD 자격 증명으로 서명된 SAS는 *사용자 위임* SAS입니다.
- 스토리지 계정 키를 사용합니다. *서비스 SAS* 와 *계정 SAS* 는 모두 스토리지 계정 키로 서명됩니다.

사용자 위임 SAS는 스토리지 계정 키로 서명된 SAS에 뛰어난 보안을 제공합니다. 가능하면 사용자 위임 SAS를 사용하는 것이 좋습니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 데이터에 대한 제한된 액세스 권한 부여](../articles/storage/common/storage-sas-overview.md)를 참조하세요.
