---
title: 사용자 흐름에 대한 API 커넥터 코드 샘플 - Azure AD
description: Azure Active Directory 외부 ID에 대한 셀프 서비스 등록 흐름의 API 커넥터에 대한 코드 샘플입니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87906935"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>외부 ID 셀프 서비스 등록 샘플

다음 표에서는 [API 커넥터](api-connectors-overview.md)를 사용하여 셀프 서비스 등록 사용자 흐름에서 웹 API를 활용하기 위한 코드 샘플에 대한 링크를 제공합니다.

## <a name="api-connector-azure-function-quickstarts"></a>API 커넥터 Azure 함수 빠른 시작

| 샘플                                                                                                                          | Description                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 이 .NET Core Azure 함수 샘플은 특정 테넌트 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다. |
| [Node.JS](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 이 Node.js Azure Function 샘플은 특정 테넌트 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 이 Python Azure 함수 샘플은 특정 테넌트 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>사용자 지정 승인 워크플로

| 샘플 | Description |
|--------| ----------- |
| [수동 승인 워크플로](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | 이 샘플에서는 셀프 서비스 등록에서 게스트 사용자 계정 만들기를 관리하는 엔드투엔드 승인 워크플로를 보여줍니다. |

## <a name="identity-verification"></a>ID 확인

| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 이 샘플에서는 API 커넥터를 사용하여 IDology와 통합함으로써 셀프 서비스 등록의 일부로 사용자 ID를 확인하는 방법을 보여줍니다. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 이 샘플에서는 API 커넥터를 사용하여 Experian과 통합함으로써 셀프 서비스 등록의 일부로 사용자 ID를 확인하는 방법을 보여줍니다. |
