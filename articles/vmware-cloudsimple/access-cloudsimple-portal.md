---
title: 클라우드심플 - 포털로 Azure VMware 솔루션 에 액세스
description: Azure Portal에서 VMware Solution by CloudSimple 포털에 액세스하는 방법 설명
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244733"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Azure 포털에서 CloudSimple 포털로 VMware 솔루션 에 액세스

CloudSimple 포털에 액세스할 수 있도록 단일 사인온이 지원됩니다. Azure 포털에 로그인한 후 다시 로그인하지 않고 CloudSimple 포털에 액세스할 수 있습니다. CloudSimple 포털에 처음 액세스하면 [CloudSimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 응용 프로그램에 대한 권한을 부여하라는 메시지가 표시됩니다.  권한 부여는 일회성 작업입니다.

## <a name="before-you-begin"></a>시작하기 전에

기본 제공 **소유자** 및 **기여자** 역할을 가진 사용자는 CloudSimple 포털에 액세스할 수 있습니다.  역할은 CloudSimple 서비스가 배포된 리소스 그룹에서 구성되어야 합니다.  역할은 CloudSimple 서비스 개체에서 구성할 수도 있습니다.  역할 확인에 대한 자세한 내용은 역할 할당 보기 문서를 [참조하세요.](https://docs.microsoft.com/azure/role-based-access-control/check-access) 기본 제공 **소유자** 및 **기여자** 역할을 가진 사용자만 CloudSimple 포털에 액세스할 수 있습니다.  역할은 구독에서 구성되어야 합니다.  역할 확인에 대한 자세한 내용은 역할 할당 보기 문서를 [참조하세요.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

사용자 지정 역할을 사용하는 경우 역할에 다음 작업 중 ```Actions```일부가 있어야 합니다.  사용자 지정 역할에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할을](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)참조합니다.  작업의 일부인 ```NotActions```경우 사용자는 CloudSimple 포털에 액세스할 수 없습니다.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스를**선택합니다.

2. 클라우드 **간단한 서비스를**검색합니다.

3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.

4. **개요** 페이지에서 **CloudSimple 포털로 이동을 클릭합니다.**  Azure 포털에서 처음으로 CloudSimple 포털에 액세스하는 경우 [CloudSimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 응용 프로그램에 대한 권한을 부여하라는 메시지가 표시됩니다. 

    ![클라우드심플 포털 출시](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Azure 포털에서 프라이빗 클라우드 만들기 또는 확장과 같은 사설 클라우드 작업을 선택하면 CloudSimple 포털이 표시된 페이지로 열립니다.

CloudSimple 포털에서 사이드 메뉴의 **홈을** 선택하여 비공개 클라우드에 대한 요약 정보를 표시합니다. 프라이빗 클라우드의 리소스와 용량은 주의가 필요한 경고 및 작업과 함께 표시됩니다. 일반적인 작업의 경우 페이지 상단의 명명된 아이콘을 클릭합니다.

![홈 페이지](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>클라우드 단순 서비스 승인 응용 프로그램에 대한 동의

Azure 포털에서 처음으로 CloudSimple 포털을 실행하려면 CloudSimple 서비스 권한 부여 응용 프로그램에 대한 동의가 필요합니다.  요청된 권한을 부여하고 CloudSimple 포털에 액세스하려면 **수락을** 선택합니다.

![클라우드 단순 서비스 권한 부여에 대한 동의 - 관리자](media/cloudsimple-azure-consent.png)

전역 관리자 권한이 있는 경우 조직에 동의할 수 있습니다.  **조직을 대신하여 동의**를 선택합니다.

![클라우드 단순 서비스 권한 부여에 대한 동의 - 글로벌 관리자](media/cloudsimple-azure-consent-global-admin.png)

사용 권한이 CloudSimple 포털에 대한 액세스를 허용하지 않는 경우 테넌트의 전역 관리자에게 문의하여 필요한 권한을 부여합니다.  글로벌 관리자는 조직을 대신하여 동의할 수 있습니다.

![CloudSimple 서비스 권한 부여에 대한 동의 - 관리자가 필요합니다.](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 를 만드는](https://docs.azure.cloudsimple.com/create-private-cloud/) 방법 알아보기
* [프라이빗 클라우드 환경 구성](quickstart-create-private-cloud.md) 방법 알아보기
