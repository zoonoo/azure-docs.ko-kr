---
title: Azure 통신 서비스에 인증
titleSuffix: An Azure Communication Services concept document
description: 응용 프로그램 또는 서비스가 통신 서비스에 인증할 수 있는 다양 한 방법에 대해 알아봅니다.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b702afe9c4359d9f8711846d93fd79df9fc2f42e
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485964"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure 통신 서비스에 인증

모든 클라이언트와 Azure 통신 서비스 간의 상호 작용을 인증 해야 합니다. 일반적인 아키텍처에서는 [클라이언트 및 서버 아키텍처](./client-and-server-architecture.md)를 참조 하 고, *액세스 키* 또는 *관리 id* 는 신뢰할 수 있는 사용자 액세스 서비스에서 사용자를 만들고 토큰을 발급 하는 데 사용 됩니다. 그리고 트러스트 된 사용자 액세스 서비스에서 발급 한 *사용자 액세스 토큰* 은 클라이언트 응용 프로그램에서 채팅 또는 호출 서비스와 같은 다른 통신 서비스에 액세스 하는 데 사용 됩니다.

또한 Azure Communication Services SMS 서비스는 인증에 대 한 *액세스 키* 또는 *관리 id* 를 허용 합니다. 이는 일반적으로 신뢰할 수 있는 서비스 환경에서 실행 되는 서비스 응용 프로그램에서 발생 합니다.

각 권한 부여 옵션에 대 한 간략 한 설명은 다음과 같습니다.

- SMS 및 Id 작업을 위한 **액세스 키** 인증입니다. 액세스 키 인증은 신뢰할 수 있는 서비스 환경에서 실행 되는 서비스 응용 프로그램에 적합 합니다. 액세스 키는 Azure Communication Services 포털에서 찾을 수 있습니다. 액세스 키를 사용 하 여 인증 하기 위해 서비스 응용 프로그램은 액세스 키를 자격 증명으로 사용 하 여 해당 SMS 또는 Id 클라이언트 라이브러리를 초기화 합니다. [액세스 토큰 만들기 및 관리](../quickstarts/access-tokens.md)를 참조 하세요. 액세스 키는 리소스의 연결 문자열에 포함 되어 있으므로 [통신 서비스 리소스 만들기 및 관리](../quickstarts/create-communication-resource.md)를 참조 하세요. 연결 문자열을 사용한 인증은 액세스 키가 있는 인증과 동일 합니다.
- SMS 및 Id 작업을 위한 **관리 되는 id** 인증 관리 [id는](../quickstarts/managed-identity.md)신뢰할 수 있는 서비스 환경에서 실행 되는 서비스 응용 프로그램에 적합 합니다. 관리 id를 사용 하 여 인증 하려면 서비스 응용 프로그램은 ID 및 관리 되는 id의 암호를 사용 하 여 자격 증명을 만든 다음 해당 SMS 또는 Id 클라이언트 라이브러리를 초기화 합니다. [액세스 토큰 만들기 및 관리](../quickstarts/access-tokens.md)를 참조 하세요.
- 채팅 및 호출에 대 한 **사용자 액세스 토큰** 인증입니다. 사용자 액세스 토큰을 통해 클라이언트 응용 프로그램은 Azure 통신 채팅 및 호출 서비스에 대해 인증할 수 있습니다. 이러한 토큰은 사용자가 만든 "신뢰할 수 있는 사용자 액세스 서비스"에서 생성 됩니다. 그런 다음 토큰을 사용 하 여 채팅을 초기화 하 고 클라이언트 라이브러리를 호출 하는 클라이언트 장치에 제공 됩니다. 자세한 내용은 [앱에 채팅 추가](../quickstarts/chat/get-started.md) (예:)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [통신 서비스 리소스](../quickstarts/create-communication-resource.md) 
>  만들기 및 관리 Azure CLI에서 Azure Active Directory [관리 id 응용 프로그램 만들기](../quickstarts/managed-identity-from-cli.md) 
>  [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)

자세한 내용은 다음 문서를 참조하세요.
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
