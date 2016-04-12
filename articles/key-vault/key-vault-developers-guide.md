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
   ms.date="03/07/2016"
   ms.author="bruceper" />

# Azure 키 자격 증명 모음 개발자 가이드

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

주요 자격 증명 모음을 사용하면 다음과 같은 응용 프로그램 내에서 중요한 정보를 안전하게 액세스할 수 있습니다.

- 키와 암호는 코드 작성 없이 보호되며 응용 프로그램에서 쉽게 사용할 수 있습니다.
- 고객들에게 고유한 키를 부여하여 관리하게 하므로 핵심 소프트웨어 기능을 제공하는 데 집중할 수 있습니다 이러한 이유로 응용 프로그램에는 고객의 테넌트 키와 암호에 대한 책임 또는 잠재적인 법적 책임이 없습니다.
- 응용 프로그램은 서명 및 암호화를 위해 키를 사용할 수 있지만, 지리적으로 분산되는 응용 프로그램에 적합한 솔루션과 같은 응용 프로그램의 경우 외부에서 키 관리를 유지할 수 있습니다.

Azure 주요 자격 증명 모음에 대한 일반적인 내용은 [주요 자격 증명 모음이란?](key-vault-whatis.md)을 참조하세요.

## 주요 자격 증명 모음 만들기 및 관리

코드에서 Azure 주요 자격 증명 모음을 사용 하여 작업하기 전에 다음 문서에 설명된 대로 나머지, 리소스 관리자 템플릿, PowerShell 또는 CLI를 통해 자격 증명 모음을 만들고 관리할 수 있습니다.

- [REST를 사용하여 주요 자격 증명 모음 만들기 및 관리](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [PowerShell을 사용하여 키 자격 증명 모음 만들기 및 관리](key-vault-get-started.md)
- [CLI를 사용하여 키 자격 증명 모음 만들기 및 관리](key-vault-manage-with-cli.md)
- [주요 자격 증명 모음 만들기 및 ARM 템플릿을 통한 암호 추가](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] 주요 자격 증명 모음에 대한 작업은 AAD를 통해 인증하고 자격 증명 모음당 정의되는 주요 자격 증명 모음의 액세스 정책을 통해 권한이 부여됩니다.

## 주요 자격 증명 모음을 사용한 코딩

프로그래머를 위한 주요 자격 증명 모음 관리 시스템은 REST를 기반으로 [주요 자격 증명 모음 REST API 참조](https://msdn.microsoft.com/library/azure/dn903609.aspx) 등 여러 인터페이스로 구성됩니다.

권한 부여의 성공에 따라 다음을 수행할 수 있습니다.

- [만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx), [가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx), [업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx), [삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) 및 기타 작업을 사용하여 암호화 키 관리

- [가져오기](https://msdn.microsoft.com/library/azure/dn903633.aspx), [업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx), [삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) 및 기타 작업을 사용하여 암호 관리

- [서명](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[확인](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) 및 [암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) 작업에 암호화 키 사용

다음 SDK는 주요 자격 증명 모음을 사용하는 작업에서 사용할 수 있습니다.

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)로 바꿉니다.|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)로 바꿉니다.
|:--:|:--:|
|[.NET SDK 설명서](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js SDK 설명서](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK 패키지](https://azure.microsoft.com/documentation/api/)|[Node.js SDK 패키지](https://www.npmjs.com/package/azure-keyvault)|


응용 프로그램에서 주요 자격 증명 모음을 사용하는 전체 예제를 참조하세요.

- .NET 샘플 응용 프로그램 *HelloKeyVault* 및 Azure 웹 서비스 예제입니다. [Azure 키 자격 증명 모음 코드 샘플](http://www.microsoft.com/download/details.aspx?id=45343)
- 자습서에서는 Azure의 웹 응용 프로그램에서 Azure 주요 자격 증명 모음을 사용하는 방법을 알아볼 수 있습니다. [웹 응용 프로그램에서 Azure 주요 자격 증명 모음 사용](key-vault-use-from-web-application.md)

## 방법

다음 문서 및 시나리오는 작업 특정 지침을 제공합니다.

- [Azure 키 자격 증명 모음용으로 HSM 보호 키를 생성하여 전송하는 방법](key-vault-hsm-protected-keys.md)
- [배포 중 보안 값(예: 암호) 전달](../resource-manager-keyvault-parameter.md).
- Azure 주요 자격 증명 모음의 통합 및 사용 방법에 대한 작업별 지침은 [주요 자격 증명 모음에 대한 Ryan Jones ARM 템플릿 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.

## 라이브러리 지원

- [Microsoft Azure 주요 자격 증명 모음 핵심 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0)는 식별자의 키를 찾고 키로 작업을 수행하기 위한 `IKey` 및 `IKeyResolver` 인터페이스를 제공합니다.

- [Microsoft Azure 키 자격 증명 모음 확장](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0)은 Azure 키 자격 증명 모음에 확장된 기능을 제공합니다.

<!---HONumber=AcomDC_0309_2016-->