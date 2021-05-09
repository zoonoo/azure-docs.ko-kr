---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89147473"
---
**기본 관리 ID** 는 시스템 할당 관리 ID 또는 첫 번째 사용자 할당 관리 ID입니다.

두 가지 ID의 애플리케이션을 실행 중입니다.

1. 시스템은 ID를 사용하여 사용자의 스토리지 탑재, 컨테이너 레지스트리 및 데이터 저장소를 설정합니다.

    * 이 경우 시스템은 기본 관리 ID를 사용합니다.

1. 사용자는 제출된 실행에 대한 코드 내에서 리소스에 액세스할 수 있도록 ID를 적용합니다.

    * 이 경우 자격 증명을 검색하는 데 사용할 관리 ID에 해당하는 *client_id* 를 제공합니다.
    * 또는 *DEFAULT_IDENTITY_CLIENT_ID* 환경 변수를 통해 사용자 할당 ID의 클라이언트 ID를 가져옵니다.

    예를 들어 기본 관리 ID를 사용하여 데이터 저장소에 대한 토큰을 검색하려면 다음을 수행합니다.

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```