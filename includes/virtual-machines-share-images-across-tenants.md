---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "73903695"
---
그러나 Azure 테넌트 외부에서 이미지를 대규모로 공유하려는 경우 쉽게 공유할 수 있도록 앱 등록을 만들어야 합니다.  앱 등록을 사용하면 다음과 같은 보다 복잡한 공유 시나리오를 사용할 수 있습니다. 

* 한 회사가 다른 회사를 인수하고 Azure 인프라가 별도의 테넌트에 분산될 때 공유 이미지를 관리합니다. 
* Azure 파트너는 고객을 대신하여 Azure 인프라를 관리합니다. 이미지 사용자 지정은 파트너 테넌트 내에서 수행되지만 인프라 배포는 고객의 테넌트에서 수행됩니다. 


## <a name="create-the-app-registration"></a>앱 등록 만들기

두 테넌트가 이미지 갤러리 리소스를 공유하는 데 사용할 애플리케이션 등록을 만듭니다.
1. [Azure Portal에서 앱 등록(미리 보기)](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)을 엽니다.    
1. 페이지 위쪽에 있는 메뉴에서 **새 등록** 을 선택합니다.
1. **이름** 에 *myGalleryApp* 을 입력합니다.
1. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** 을 선택합니다.
1. **리디렉션 URI** 에서 *https://www.microsoft.com* 을 입력한 다음, **등록** 을 선택합니다. 앱 등록이 생성되면 개요 페이지가 열립니다.
1. 개요 페이지에서 **애플리케이션(클라이언트) ID** 를 복사하고 나중에 사용할 수 있도록 저장합니다.   
1. **인증서 및 비밀** 을 선택하고 **새 클라이언트 암호** 를 선택합니다.
1. **설명** 에 *공유 이미지 갤러리 교차 테넌트 앱 비밀* 을 입력합니다.
1. **만료** 에서 기본값인 **1년** 을 그대로 둔 다음, **추가** 를 선택합니다.
1. 비밀 값을 복사하여 안전한 장소에 저장합니다. 페이지를 나간 후에는 검색할 수 없습니다.


공유 이미지 갤러리를 사용하려면 앱 등록 권한을 부여합니다.
1. Azure Portal에서 다른 테넌트와 공유하려는 Shared Image Gallery를 선택합니다.
1. **액세스 제어(IAM) 선택** 을 선택하고 **역할 할당 추가** 에서 *추가* 를 선택합니다. 
1. **역할** 에서 **읽기 권한자** 를 선택합니다.
1. **다음에 대한 액세스 할당:** 에서 이 항목을 **Azure AD 사용자, 그룹 또는 서비스 주체** 로 둡니다.
1. **선택** 에서 *myGalleryApp* 을 입력하고 목록에 표시되면 선택합니다. 작업이 완료되면 **저장** 을 선택합니다.


## <a name="give-tenant-2-access"></a>테넌트 2에 액세스 권한 부여

브라우저를 사용하여 로그인을 요청하여 테넌트 2에 애플리케이션에 대한 액세스 권한을 부여합니다. *\<Tenant2 ID>* 를 이미지 갤러리를 공유하려는 테넌트의 테넌트 ID로 바꿉니다. *\<Application (client) ID>* 를 생성한 앱 등록의 애플리케이션 ID로 바꿉니다. 교체를 완료한 후 URL을 브라우저에 붙여넣고 로그인 프롬프트에 따라 테넌트 2에 로그인합니다.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

[Azure Portal](https://portal.azure.com)에서 테넌트 2로 로그인하고 VM을 만들려는 리소스 그룹에 앱 등록 액세스 권한을 부여합니다.

1. 리소스 그룹을 선택한 다음, **액세스 제어(IAM)** 를 선택합니다. **역할 할당 추가** 에서 **추가** 를 선택합니다. 
1. **역할** 에서 **기여자** 를 입력합니다.
1. **다음에 대한 액세스 할당:** 에서 이 항목을 **Azure AD 사용자, 그룹 또는 서비스 주체** 로 둡니다.
1. **선택** 에서 *myGalleryApp* 을 입력하고 목록에 표시되면 선택합니다. 작업이 완료되면 **저장** 을 선택합니다.

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.

