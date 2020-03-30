---
title: 아즈카피 로그인 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 로그인 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295400"
---
# <a name="azcopy-login"></a>azcopy login

Azure Active Directory에 로그인하여 Azure 저장소 리소스에 액세스합니다.

## <a name="synopsis"></a>개요

Azure Active Directory에 로그인하여 Azure 저장소 리소스에 액세스합니다.

Azure Storage 계정에 권한을 부여하려면 저장소 계정, 상위 리소스 그룹 또는 상위 구독의 컨텍스트에서 **저장소 Blob 데이터 기여자** 역할을 사용자 계정에 할당해야 합니다.

이 명령은 OS 기본 제공 메커니즘을 사용하여 현재 사용자의 암호화된 로그인 정보를 캐시합니다.

자세한 내용은 예제를 참조하십시오.

> [!IMPORTANT]
> 명령줄을 사용하여 환경 변수를 설정하면 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록의 자격 증명이 포함된 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않도록 하려면 스크립트를 사용하여 사용자에게 자격 증명을 묻고 환경 변수를 설정할 수 있습니다.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

기본 AAD 테넌트 ID를 공통으로 설정하여 대화식으로 로그인합니다.

```azcopy
azcopy login
```

지정된 테넌트 ID로 대화식으로 로그인합니다.

```azcopy
azcopy login --tenant-id "[TenantID]"
```

VM(가상 컴퓨터)의 시스템 할당 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity
```

VM의 사용자 할당 ID와 서비스 ID의 클라이언트 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

VM의 사용자 할당 ID와 서비스 ID의 개체 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

VM의 사용자 할당 ID와 서비스 ID의 리소스 ID를 사용하여 로그인합니다.

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

클라이언트 비밀을 사용하여 서비스 주체로 로그인합니다. 환경 변수 AZCOPY_SPA_CLIENT_SECRET 비밀 기반 서비스 주체 인증에 대 한 클라이언트 보안 에 설정 합니다.

```azcopy
azcopy login --service-principal
```

인증서 와 암호를 사용하여 서비스 주체로 로그인합니다. 인증서 기반 서비스 주체 권한 부여에 대한 인증서 암호로 환경 변수 AZCOPY_SPA_CERT_PASSWORD 설정합니다.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

/path/to/my/cert를 PEM 또는 PKCS12 파일의 경로로 처리해야 합니다. AzCopy는 인증서를 얻기 위해 시스템 인증서 저장소에 도달하지 않습니다.

--인증서 경로는 인증서 기반 서비스 주체 인증을 수행할 때 필수입니다.

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|--aad-엔드포인트|사용할 Azure Active 디렉터리 끝점입니다. 기본값`https://login.microsoftonline.com`()은 공용 Azure 클라우드에 적합합니다. 국가 클라우드에서 인증할 때 이 매개 변수를 설정합니다. [Azure AD 인증 끝점을](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)참조하십시오.
관리되는 서비스 ID에는 이 플래그가 필요하지 않습니다.|
|--응용 프로그램 ID 문자열|사용자 할당된 ID의 응용 프로그램 ID입니다. 서비스 주 체에 필요한 경우입니다.|
|--인증서 경로 문자열|SPN 인증을 위한 인증서 경로입니다. 인증서 기반 서비스 주체 인증에 필요합니다.|
|-h, --help|로그인 명령에 대한 도움말 콘텐츠를 표시합니다.|
|--ID|MSI(관리 서비스 ID)라고도 하는 가상 머신의 ID를 사용하여 로그인합니다.|
|--ID-클라이언트-ID 문자열|사용자 할당된 ID의 클라이언트 ID입니다.|
|--ID-개체 ID 문자열|사용자 할당된 ID의 개체 ID입니다.|
|--ID-리소스 ID 문자열|사용자가 할당한 ID의 리소스 ID입니다.|
|--서비스 주체|인증서 또는 비밀을 사용하여 SPN(서비스 주체 이름)을 통해 로그인합니다. 클라이언트 암호 또는 인증서 암호를 적절한 환경 변수에 배치해야 합니다. 환경 `AzCopy env` 변수의 이름과 설명을 보려면 입력합니다.|
|--테넌트 ID 문자열| OAuth 장치 대화형 로그인에 사용할 Azure 활성 디렉터리 테넌트 ID입니다.|

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

|옵션|설명|
|---|---|
|--캡 mbps uint32|초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.|
|--출력 유형 문자열|명령 출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 "텍스트"입니다.|

## <a name="see-also"></a>참조

- [아즈카피](storage-ref-azcopy.md)
