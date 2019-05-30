---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115513"
---
> [!IMPORTANT]
> 저장소 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 항상 계정 키를 보호해야 합니다. 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사람이 액세스할 수 있는 일반 텍스트로 아무 곳이나 저장하지 마세요. 손상되었다고 생각되면 Azure Portal을 사용하여 계정 키를 다시 생성합니다.
> 
> SAS(공유 액세스 서명) 토큰은 계정 액세스 키처럼 보호에 중요한 역할을 합니다. 세분성을 제공할 때 SAS는 클라이언트에 스토리지 계정의 리소스에 대한 액세스 권한을 부여하며 공개적으로 공유하면 안 됩니다. 문제 해결을 위해 공유해야 하는 경우 수정된 로그 파일 버전을 사용하거나 로그 파일에서 SAS 토큰을 삭제하고(있는 경우), 스크린샷에 SAS 정보가 포함되지 않게 해야 합니다.
> 
> 보안 강화를 위해 가능한 경우 Blob 및 Queue Storage 응용 프로그램에 대해 Azure AD(Azure Active Directory) 인증을 사용하는 것이 좋습니다(미리 보기). 자세한 내용은 [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 인증(미리 보기)](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)을 참조하세요.
