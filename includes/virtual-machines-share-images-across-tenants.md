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
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145895"
---
공유 이미지 갤러리에서는 RBAC를 사용 하 여 공유 이미지 있습니다. RBAC를 사용 하 여 테 넌 트 외부의 개인을 테 넌 트 내에서 이미지를 공유할 수 있습니다. 하지만 Azure 테 넌 트를 대규모로 외부에 있는 이미지를 공유 하려는 경우 공유를 용이 하 게 하려면 앱 등록을 만들어야 합니다.  앱 등록을 사용 하 여 같은 복잡 한 공유 시나리오를 설정할 수 있습니다. 

* 하나의 회사 다른를 획득 하 고 별도 테 넌 트에 걸쳐 Azure 인프라는 경우 이미지를 공유 관리 합니다. 
* Azure 파트너는 고객을 대신 하 여 Azure 인프라를 관리 합니다. 사용자 지정 이미지의 파트너 테 넌 트 내에서 수행 됩니다 있지만 고객의 테 넌 트에 인프라 배포 현상이 발생 합니다. 


## <a name="create-the-app-registration"></a>앱 등록 만들기

이미지 갤러리 리소스를 공유 하도록 두 테 넌 트에서 사용할 수 있는 응용 프로그램 등록을 만듭니다.
1. 엽니다는 [Azure portal에서 앱 등록 (미리 보기)](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)합니다.    
1. 선택 **새 등록** 페이지의 맨 위에 있는 메뉴에서.
1. **이름을**, 형식 *myGalleryApp*합니다.
1. **지원 되는 계정 유형**를 선택 **계정이 모든 조직 디렉터리에 개인 Microsoft 계정**합니다.
1. **리디렉션 URI**, 형식 *https://www.microsoft.com* 선택한 후 **등록**합니다. 앱 등록 만들어지면 개요 페이지가 열립니다.
1. 개요 페이지에서 복사 합니다 **응용 프로그램 (클라이언트) ID** 하 고 나중에 사용 하기 위해 저장 합니다.   
1. 선택 **인증서 및 비밀**를 선택한 후 **새 클라이언트 암호**합니다.
1. **설명을**, 형식 *공유 이미지 갤러리 교차 테 넌 트 앱 비밀*합니다.
1. **Expires**, 기본값인 **에 1 년** 선택한 후 **추가**합니다.
1. 비밀의 값을 복사 하 고 안전한 장소에 저장 합니다. 페이지를 나가면 검색할 수 없습니다.


공유 이미지 갤러리를 사용 하도록 앱 등록 권한을 부여 합니다.
1. Azure portal에서 다른 테 넌 트와 공유 하려는 공유 이미지 갤러리를 선택 합니다.
1. 선택 **액세스 제어 (IAM)를 선택**, 아래에서 **역할 할당 추가** 선택 *추가*합니다. 
1. 아래 **역할**를 선택 **판독기**합니다.
1. 아래 **에 대 한 액세스 할당:**, 기본값을 그대로 **Azure AD 사용자, 그룹 또는 서비스 주체**합니다.
1. 아래 **선택**, 형식 *myGalleryApp* 목록에 표시 되 면 선택 합니다. 완료 되 면 선택 **저장할**합니다.


## <a name="give-tenant-2-access"></a>테 넌 트 2 대 한 액세스를 제공 합니다.

브라우저를 사용 하 여 로그인을 요청 하 여 응용 프로그램에 테 넌 트 2 대 한 액세스를 제공 합니다. 바꿉니다 *<Tenant2 ID>* 이미지 갤러리를 공유 하려는 테 넌 트에 대 한 테 넌 트 ID를 사용 하 여 합니다. 바꿉니다 *< 응용 프로그램 (클라이언트) ID >* 만든 앱 등록의 응용 프로그램 ID를 사용 하 여 합니다. 마친 대체를 브라우저에 URL을 붙여넣습니다 및 테 넌 트 2에 로그인 하려면 로그인 프롬프트를 따릅니다.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

에 [Azure portal](https://portal.azure.com) 테 넌 트 2로 로그인 하 고 VM를 만들려는 리소스 그룹에 앱 등록 액세스를 제공 합니다.

1. 리소스 그룹을 선택 하 고 선택한 **액세스 제어 (IAM)** 합니다. 아래 **역할 할당 추가** 선택 **추가**합니다. 
1. 아래 **역할**, 형식 **참가자**합니다.
1. 아래 **에 대 한 액세스 할당:**, 기본값을 그대로 **Azure AD 사용자, 그룹 또는 서비스 주체**합니다.
1. 아래 **선택** 형식을 *myGalleryApp* 목록에 표시 되 면 선택 합니다. 완료 되 면 선택 **저장할**합니다.

> [!NOTE]
> 이미지 버전을 빌드하고 같은 관리 되는 이미지를 사용 하 여 다른 이미지 버전을 만들려면 전에 복제를 완전히 완료 될 때까지 기다리는 해야 합니다.

