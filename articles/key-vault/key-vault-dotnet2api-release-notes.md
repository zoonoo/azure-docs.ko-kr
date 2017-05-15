---
title: "Key Vault .NET 2.x API 릴리스 정보 | Microsoft Docs"
description: ".NET 개발자는 Azure Key Vault에 대한 코드에 이 API를 사용합니다."
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 21b34e30a7e07b37ccae3f101f2ab4dfadfad9bf
ms.contentlocale: ko-kr
ms.lasthandoff: 04/29/2017


---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - 릴리스 정보 및 마이그레이션 가이드
다음 참고 사항과 지침은 Azure Key Vault .NET/C# 라이브러리로 작업하는 개발자를 위한 것입니다. 1.0 버전에서 2.0 버전으로 전환되면 업데이트할 경우 대부분 **Key Vault 인증서** 지원과 같은 기능 향상 및 추가 기능의 이점을 제공하기 위해 코드에서 마이그레이션 작업을 수행해야 했습니다.

## <a name="key-vault-certificates"></a>Key Vault 인증서

Key Vault 인증서 지원은 x509 인증서 및 다음 동작의 관리를 위해 제공됩니다.  

* 인증서 소유자는 Key Vault 만들기 프로세스 또는 기존 인증서의 가져오기를 통해 인증서를 만들 수 있습니다. 여기에는 자체 서명된 인증서 및 인증 기관 생성 인증서가 모두 포함됩니다.
* Key Vault 인증서 소유자는 개인 키 자료와 상호 작용하지 않고 X509 인증서의 안전한 저장 및 관리를 구현할 수 있습니다.  
* 인증서 소유자는 인증서의 수명 주기를 관리하도록 Key Vault를 지시하는 정책을 만들 수 있습니다.  
* 인증서 소유자는 인증서의 만료 및 갱신이라는 수명 주기 이벤트에 대한 알림을 위해 연락처 정보를 제공할 수 있습니다.  
* 선택한 발급자 - Key Vault 파트너 X509 인증서 공급자/인증서 기관을 통한 자동 갱신을 지원합니다.
  
  * 참고 - 비협력 공급자/기관도 갱신을 허용하지만 자동 갱신 기능을 지원하지 않습니다.

## <a name="net-support"></a>.NET 지원

* **.NET 4.0**은 Azure Key Vault .NET/C# 라이브러리의 2.0 버전에서 지원되지 않습니다.
* **.NET Core**는 Azure Key Vault .NET/C# 라이브러리의 2.0 버전에서 지원되지 않습니다.

## <a name="namespaces"></a>네임스페이스

* **모델**의 네임스페이스는 **Microsoft.Azure.KeyVault**에서 **Microsoft.Azure.KeyVault.Models**로 변경됩니다.
* **Microsoft.Azure.KeyVault.Internal** 네임스페이스는 삭제됩니다.
* Azure SDK 종속성 네임스페이스는 **Hyak.Common** 및 **Hyak.Common.Internals**에서 **Microsoft.Rest** 및 **Microsoft.Rest.Serialization**로 변경됩니다.

## <a name="type-changes"></a>유형 변경

* *비밀*을 *SecretBundle*로 변경
* *사전*을 *IDictionary*로 변경
* *목록<T>, string []*을 *IList<T>*로 변경
* *NextList*를 *NextPageLink*로 변경

## <a name="return-types"></a>반환 유형

* **KeyList** 및 **SecretList**는 *ListKeysResponseMessage* 대신 *IPage<T>*를 반환합니다.
* 생성된 **BackupKeyAsync**는 *값*(백업 blob)을 포함하는 *BackupKeyResult*를 반환합니다. 메서드 줄 바꿈 전에 값을 반환합니다.

## <a name="exceptions"></a>예외

* *KeyVaultClientException*를 *KeyVaultErrorException*로 변경합니다.
* 서비스 오류는 *exception.Error*에서 *exception.Body.Error.Message*로 변경됩니다.
* **[JsonExtensionData]**에 대한 오류 메시지에서 추가 정보를 제거합니다.

## <a name="constructors"></a>생성자

* *HttpClient*를 생성자 인수로 적용하는 대신 생성자는 *HttpClientHandler* 또는 *DelegatingHandler*만 허용합니다.

## <a name="downloaded-packages"></a>다운로드한 패키지

클라이언트가 Key Vault에 대한 종속성을 처리하는 경우 다음을 다운로드합니다.

### <a name="previous-package-list"></a>이전 패키지 목록

* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>현재 패키지 목록

* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>클래스 변경

* **UnixEpoch** 클래스가 제거되었습니다.
* **Base64UrlConverter** 클래스 이름을 **Base64UrlJsonConverter**로 변경합니다.

## <a name="other-changes"></a>기타 변경 내용

* 일시적 오류가 발생한 경우 KV 작업 재시도 정책의 구성에 대한 지원은 이 버전의 API에 추가되었습니다.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* *자격 증명 모음*을 반환하는 작업의 경우 반환 유형은 자격 증명 모음 속성을 포함하는 클래스였습니다. 반환 유형은 이제 *자격 증명 모음*입니다.
* *PermissionsToKeys* 및 *PermissionsToSecrets*는 이제 *Permissions.Keys* 및 *Permissions.Secrets*입니다.
* 반환 형식 변경 내용 중 일부는 제어 평면에도 적용됩니다.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* 패키지는 암호화 작업에 대해 **Microsoft.Azure.KeyVault.Extensions** 및 **Microsoft.Azure.KeyVault.Cryptography**로 나눠집니다.


