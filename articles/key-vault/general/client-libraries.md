---
title: Azure Key Vault용 클라이언트 라이브러리 | Microsoft Docs
description: Azure Key Vault용 클라이언트 라이브러리
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 2c48917e7205bca03380267c1c97469acc245fa6
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628030"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault용 클라이언트 라이브러리

Azure Key Vault용 클라이언트 라이브러리를 사용하면 .NET, Python, Java 및 JavaScript를 비롯한 다양한 언어에서 Key Vault 기능에 프로그래밍 방식으로 액세스할 수 있습니다.

## <a name="client-libraries-per-language-and-object"></a>언어 및 개체당 클라이언트 라이브러리

각 SDK에는 아래 표에 따라 키 자격 증명 모음, 비밀, 키 및 인증서에 대한 별도의 클라이언트 라이브러리가 있습니다.

| 언어 | 비밀 | 구성 | 인증서 | Key Vault(관리 평면)
|--|--|--|--|
| .NET | - [API 참조](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [빠른 시작](../secrets/quick-create-net.md) | - [API 참조](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API 참조](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) | - [API 참조](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API 참조](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-secrets/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-python.md) |- [API 참조](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-keys/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [빠른 시작](../keys/quick-create-python.md) | - [API 참조](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-certificates/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [빠른 시작](../certificates/quick-create-python.md) | - [API 참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [PyPi 패키지](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-java.md) |- [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |- [API 참조](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API 참조](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-node.md) |- [API 참조](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API 참조](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |  - [API 참조](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개발자 가이드](developers-guide.md) 참조
- [Key Vault에 인증](authentication.md)에 대해 자세히 알아보세요.
- [Key Vault에 대한 액세스 보안](secure-your-key-vault.md)에 대해 자세히 알아보세요.
