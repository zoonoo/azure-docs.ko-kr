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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903695"
---
그러나 Azure 테넌트 외부에서 이미지를 공유하려면 대규모로 공유를 용이하게 하기 위해 앱 등록을 만들어야 합니다.  앱 등록을 사용하면 다음과 같은 보다 복잡한 공유 시나리오를 사용할 수 있습니다. 

* 한 회사가 다른 회사를 인수할 때 공유 이미지를 관리하고 Azure 인프라가 별도의 테넌트와 분산됩니다. 
* Azure 파트너는 고객을 대신하여 Azure 인프라를 관리합니다. 이미지 사용자 지정은 파트너 테넌트 내에서 수행되지만 인프라 배포는 고객의 테넌트에서 수행됩니다. 


## <a name="create-the-app-registration"></a>앱 등록 만들기

두 테넌자에서 이미지 갤러리 리소스를 공유하는 데 사용할 응용 프로그램 등록을 만듭니다.
1. Azure [포털에서 앱 등록(미리 보기)을](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)엽니다.    
1. 페이지 상단의 메뉴에서 **새 등록을** 선택합니다.
1. **이름에서** *myGalleryApp*를 입력합니다.
1. **지원되는 계정 유형에서** **조직 디렉터리 및 개인 Microsoft 계정에서 계정을**선택합니다.
1. **URI 리디렉션에서**을 *https://www.microsoft.com* 입력한 다음 **등록을**선택합니다. 앱 등록이 만들어지면 개요 페이지가 열립니다.
1. 개요 페이지에서 **응용 프로그램(클라이언트) ID를** 복사하고 나중에 사용할 수 위해 저장합니다.   
1. **인증서 & 비밀을**선택한 다음 새 클라이언트 **보안**입니다.
1. **설명에서** *공유 이미지 갤러리 교차 테넌트 앱 비밀을*입력합니다.
1. **만료에서**기본값인 **1년을** 그대로 두고 **추가를**선택합니다.
1. 비밀의 값을 복사하여 안전한 장소에 저장합니다. 페이지를 나면 검색할 수 없습니다.


앱 등록 권한을 부여하여 공유 이미지 갤러리를 사용할 수 있습니다.
1. Azure 포털에서 다른 테넌트와 공유할 공유 이미지 갤러리를 선택합니다.
1. **IAM(액세스 제어)을 선택하고** **역할 할당 추가에서** *추가*를 선택합니다. 
1. **역할**에서 **읽기를 선택합니다.**
1. **에 대한 액세스 할당에서**에 대한 액세스 권한은 Azure **AD 사용자, 그룹 또는 서비스 주체로**둡니다.
1. **선택**에서 *myGalleryApp를* 입력하고 목록에 표시 될 때 선택합니다. 작업이 완료되면 **저장**을 선택합니다.


## <a name="give-tenant-2-access"></a>테넌트 2 액세스 권한 부여

브라우저를 사용하여 로그인을 요청하여 테넌트 2응용 프로그램에 대한 액세스 권한을 부여합니다. 이미지 갤러리를 공유하려는 테넌트의 테넌트 ID로 * \<tenant2 ID>* 대체합니다. * \<응용 프로그램(클라이언트) ID>* 만든 앱 등록의 응용 프로그램 ID로 바꿉니다. 교체가 완료되면 URL을 브라우저에 붙여넣고 로그인 프롬프트를 따라 테넌트 2에 로그인합니다.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Azure [Portal에서](https://portal.azure.com) 테넌트 2로 로그인하고 VM을 만들 리소스 그룹에 대한 앱 등록 액세스를 제공합니다.

1. 리소스 그룹을 선택한 다음 **IAM(액세스 제어)을**선택합니다. **역할 할당 추가에서** **추가**를 선택합니다. 
1. **역할**에서 **기여자**.
1. **에 대한 액세스 할당에서**에 대한 액세스 권한은 Azure **AD 사용자, 그룹 또는 서비스 주체로**둡니다.
1. **선택** 유형 *에서 myGalleryApp* 다음 목록에 표시 될 때 선택 합니다. 작업이 완료되면 **저장**을 선택합니다.

> [!NOTE]
> 동일한 관리되는 이미지를 사용하여 다른 이미지 버전을 만들려면 이미지 버전이 빌드 및 복제가 완전히 완료될 때까지 기다려야 합니다.

