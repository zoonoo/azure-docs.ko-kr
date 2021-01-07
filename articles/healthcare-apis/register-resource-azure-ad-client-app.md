---
title: Azure AD에서 리소스 앱 등록-FHIR 용 Azure API
description: 클라이언트 응용 프로그램이 인증할 때 리소스에 대 한 액세스를 요청할 수 있도록 Azure Active Directory에 리소스 (또는 API) 앱을 등록 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024485"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Azure Active Directory에서 리소스 응용 프로그램 등록

이 문서에서는 리소스 (또는 API) 응용 프로그램을 Azure Active Directory에 등록 하는 방법을 알아봅니다. 리소스 응용 프로그램은 FHIR 서버 API 자체의 Azure Active Directory 표현이 며 클라이언트 응용 프로그램은 인증할 때 리소스에 대 한 액세스를 요청할 수 있습니다. 리소스 응용 프로그램은 OAuth 용어의 *대상 사용자* 라고도 합니다.

## <a name="azure-api-for-fhir"></a>FHIR용 Azure API

FHIR 용 Azure API를 사용 하는 경우 서비스를 배포할 때 리소스 응용 프로그램이 자동으로 만들어집니다. 응용 프로그램을 배포할 때와 동일한 Azure Active Directory 테 넌 트에 Azure API 용 Azure API를 사용 하는 경우이 방법 가이드를 건너뛰고 대신 Azure API를 배포 하 여 시작 하도록 할 수 있습니다.

구독과 연결 되지 않은 다른 Azure Active Directory 테 넌 트를 사용 하는 경우 PowerShell을 사용 하 여 FHIR 리소스 응용 프로그램에 대 한 Azure API를 테 넌 트로 가져올 수 있습니다.

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

또는 Azure CLI를 사용할 수 있습니다.

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Azure 용 FHIR 서버

Azure 용 오픈 소스 FHIR 서버를 사용 하는 경우 [GitHub 리포지토리의](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) 단계에 따라 리소스 응용 프로그램을 등록 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에서 리소스 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 그런 다음 기밀 클라이언트 응용 프로그램을 등록 합니다.
 
>[!div class="nextstepaction"]
>[기밀 클라이언트 응용 프로그램 등록](register-confidential-azure-ad-client-app.md)