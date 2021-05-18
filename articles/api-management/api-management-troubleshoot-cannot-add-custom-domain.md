---
title: Key Vault 인증서를 사용하여 사용자 지정 도메인을 추가할 수 없음
titleSuffix: Azure API Management
description: 키 자격 증명 모음 인증서를 사용하여 Azure API Management에서 사용자 지정 도메인을 추가할 수 없는 문제 해결 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75430589"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>API Management 서비스 호스트 이름 업데이트 실패

이 문서에서는 Azure API Management 서비스에 대한 사용자 지정 도메인을 추가할 때 발생할 수 있는 "API Management 서비스 호스트 이름을 업데이트하지 못했습니다" 오류에 대해 설명합니다. 이 문서에서는 해당 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다.

## <a name="symptoms"></a>증상

Azure Key Vault의 인증서를 사용하여 API Management 서비스에 대한 사용자 지정 도메인을 추가하려고 하면 다음 오류 메시지가 나타납니다:

- API Management 서비스 호스트 이름 업데이트에 실패했습니다. 'https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 ' 리소스에 대한 요청이 StatusCode: RequestId에 대해 금지됨으로 인해 실패했습니다. 예외 메시지: 작업에서 잘못된 상태 코드 '금지됨'을 반환했습니다.

## <a name="cause"></a>원인

API Management 서비스에 사용자 지정 도메인에 사용하려는 키 자격 증명 모음에 액세스 할 수 있는 권한이 없습니다.

## <a name="solution"></a>솔루션

이 문제를 해결하려면 다음 단계를 따릅니다.

1. [Azure portal](Https://portal.azure.com)로 이동하여 API Management 인스턴스를 선택한 다음 **관리 ID** 를 선택합니다. **Azure Active Directory에 등록** 옵션이 **예** 로 설정되어 있는지 확인합니다. 
    ![Azure Active Directory에 등록하는 중](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Azure Portal에서 **Key Vault** 서비스를 열고 사용자 지정 도메인에 사용하려는 키 자격 증명 모음을 선택합니다.
1. **액세스 정책** 을 선택하고 API Management 서비스 인스턴스의 이름과 일치하는 서비스 주체가 있는지 확인합니다. 있는 경우 서비스 주체를 선택하고 **비밀 권한** 아래에 나열된 **가져오기** 권한이 있는지 확인합니다.  
    ![서비스 주체에 대한 액세스 정책 추가](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. API Management 서비스가 목록에 없는 경우 **액세스 정책 추가** 를 선택한 후 다음 액세스 정책을 만듭니다:
    - **템플릿에서 구성**: 없음
    - **주체 선택**: API Management 서비스의 이름을 검색한 다음 목록에서 선택
    - **키 사용 권한**: 없음
    - **비밀 권한**: 가져오기
    - **인증서 권한**: 없음
1. **확인** 을 선택하여 액세스 정책을 만듭니다.
1. **저장** 을 선택하여 변경 내용을 저장합니다.

문제가 해결되었는지 확인합니다. 이렇게 하려면 Key Vault 인증서를 사용하여 API Management 서비스에서 사용자 지정 도메인을 만들어 보세요.

## <a name="next-steps"></a>다음 단계
API Management 서비스에 대한 자세한 정보:

- API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.

* [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)
* [첫 번째 API 관리](import-and-publish.md)