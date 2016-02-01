<properties
   pageTitle="키 자격 증명 모음 개발자 가이드 | Microsoft Azure"
   description="개발자는 Microsoft Azure 환경 내에서 Azure 키 자격 증명 모음을 사용하여 암호화 키를 관리할 수 있습니다."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="1/19/2016"
   ms.author="bruceper" />

# Azure 키 자격 증명 모음 개발자 가이드

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

개발자로서는 Microsoft Azure 환경 내에서 Azure 키 자격 증명 모음을 사용하여 암호화 키를 관리할 수 있습니다. 키 자격 증명 모음은 여러 키 유형 및 알고리즘을 지원하고 중요한 키에 HSM(하드웨어 보안 모듈)과 함께 사용될 수 있습니다. 또한 키 자격 증명 모음을 사용하여 특정 의미 체계가 없는 제한된 크기의 옥텟 개체인 암호를 안전하게 저장할 수 있습니다. 개체 형식에 대한 액세스 제어는 독립적으로 관리됩니다.

권한 부여의 성공에 따라 다음을 수행할 수 있습니다.

- [만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx), [가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx), [업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx), [삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) 및 기타 작업을 사용하여 암호화 키 관리

- [가져오기](https://msdn.microsoft.com/library/azure/dn903633.aspx), [업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx), [삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) 및 기타 작업을 사용하여 비밀 관리

- [서명](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[확인](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) 및 [암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) 작업에 암호화 키 사용

키 자격 증명 모음에 대한 작업은 Azure Active Directory를 통해 인증 및 권한 부여됩니다.

## 키 자격 증명 모음에 대한 프로그래밍

프로그래머를 위한 키 자격 증명 모음 관리 시스템은 REST를 기반으로 하여 여러 인터페이스로 구성됩니다. [주요 자격 증명 모음 REST API 참조](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)로 바꿉니다.|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)로 바꿉니다.
|:--:|:--:|
|[.NET SDK 설명서](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js SDK 설명서](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK 패키지](https://azure.microsoft.com/ko-KR/documentation/api/)|[Node.js SDK 패키지](https://www.npmjs.com/package/azure-keyvault)|

## 키 자격 증명 모음 관리

Azure 주요 자격 증명 모음 컨테이너(자격 증명 모음)는 다음 문서에 설명된 대로 REST, PowerShell 또는 CLI를 사용하여 관리할 수 있습니다.

- [REST를 사용하여 주요 자격 증명 모음 만들기 및 관리](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [PowerShell을 사용하여 키 자격 증명 모음 만들기 및 관리](key-vault-get-started.md)
- [CLI를 사용하여 키 자격 증명 모음 만들기 및 관리](key-vault-manage-with-cli.md)


## 방법

다음 문서는 작업 특정 지침을 제공합니다.

- [Azure 키 자격 증명 모음용으로 HSM 보호 키를 생성하여 전송하는 방법](key-vault-hsm-protected-keys.md)

## 예

- 이 다운로드는 HelloKeyVault 샘플 응용 프로그램 및 Azure 웹 서비스 예제를 모두 포함합니다. [Azure 키 자격 증명 모음 코드 샘플](http://www.microsoft.com/download/details.aspx?id=45343)
- 이 자습서에서는 Azure의 웹 응용 프로그램에서 Azure 주요 자격 증명 모음을 사용하는 방법을 알아볼 수 있습니다. [웹 응용 프로그램에서 Azure 주요 자격 증명 모음 사용](key-vault-use-from-web-application.md)

## 라이브러리 지원

- [Microsoft Azure 키 자격 증명 모음 핵심 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0)는 식별자의 키를 찾고 키로 작업을 수행하기 위한 IKey 및 IKeyResolver 인터페이스를 제공합니다.

- [Microsoft Azure 키 자격 증명 모음 확장](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0)은 Azure 키 자격 증명 모음에 확장된 기능을 제공합니다.

<!---HONumber=AcomDC_0121_2016-->