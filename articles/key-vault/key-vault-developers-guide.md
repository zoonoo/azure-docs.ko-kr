<properties
   pageTitle="주요 자격 증명 모음 개발자 가이드 | Microsoft Azure"
   description="개발자는 Microsoft Azure 환경 내에서 Azure 주요 자격 증명 모음을 사용하여 암호화 키를 관리할 수 있습니다."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Azure 주요 자격 증명 모음 개발자 가이드

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

개발자는 Microsoft Azure 환경 내에서 Azure 주요 자격 증명 모음을 사용하여 암호화 키를 관리할 수 있습니다. 주요 자격 증명 모음은 여러 키 유형 및 알고리즘을 지원하고 중요한 고객 키에 HSM(하드웨어 보안 모듈)과 함께 사용될 수 있습니다. 또한 주요 자격 증명 모음을 사용하여 특정 의미 체계가 없는 제한된 크기의 옥텟 개체인 암호를 안전하게 저장할 수 있습니다. 주요 자격 증명 모음은 키와 비밀의 혼합을 포함할 수 있습니다. 개체 형식에 대한 액세스 제어는 독립적으로 관리됩니다.

사용자는 권한 부여의 성공에 따라 다음을 수행할 수 있습니다.

- [만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx), [가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx), [업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx), [삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) 및 기타 작업을 사용하여 암호화 키 관리

- [가져오기](https://msdn.microsoft.com/library/azure/dn903633.aspx), [업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx, [삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) 및 기타 작업을 사용하여 비밀 관리

- [서명](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[확인](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) 및 [암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) 작업에 암호화 키 사용

주요 자격 증명 모음에 대한 작업은 Azure Active Directory를 사용하여 인증 및 권한 부여됩니다.

## 주요 자격 증명 모음에 대한 프로그래밍

프로그래머를 위한 주요 자격 증명 모음 관리 시스템은 REST를 기반으로 하여 여러 인터페이스로 구성됩니다.

### REST (영문)

REST API는 주요 자격 증명 모음에 대한 모든 프로그래밍 방식 조작의 기반입니다.

주요 자격 증명 모음에는 자체 REST 끝점이 있으며 이는 [주요 자격 증명 모음 REST API 참조](https://msdn.microsoft.com/library/azure/dn903609.aspx)에 설명되어 있습니다.

### .NET

.NET API는 직접 REST 끝점을 조작할 필요 없이 C# 프로그래밍 모델을 통한 구현을 허용하는 래퍼 집합입니다. 여기서 [Azure 주요 자격 증명 모음 .NET 클라이언트 API 참조](https://msdn.microsoft.com/library/azure/dn903301.aspx)를 찾을 수 있습니다.

### Node.js

Node.js API는 직접 REST 끝점을 조작할 필요 없이 JavaScript 프로그래밍 모델을 통한 구현을 허용하는 래퍼 집합입니다. 여기서 [Node.js용 Microsoft Azure SDK - 주요 자격 증명 모음 관리](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)를 찾을 수 있습니다.

## PowerShell 및 CLI를 사용하여 주요 자격 증명 모음 관리

Azure 주요 자격 증명 모음 키 및 비밀은 다음 문서에 설명되어 있는 것처럼 PowerShell 및 CLI를 사용해서도 관리할 수 있습니다.

- [PowerShell을 사용하여 주요 자격 증명 모음 만들기 및 관리](key-vault-get-started.md)
- [CLI를 사용하여 주요 자격 증명 모음 만들기 및 관리](key-vault-manage-with-cli.md)
- [Azure 주요 자격 증명 모음용으로 HSM 보호 키를 생성하여 전송하는 방법](key-vault-hsm-protected-keys.md)
- [키 및 비밀 정보](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## 참고 항목

- [Azure 주요 자격 증명 모음 코드 샘플](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Sept15_HO4-->