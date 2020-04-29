---
title: CloudSimple로 Azure VMware 솔루션 액세스-포털
description: Azure Portal에서 VMware Solution by CloudSimple 포털에 액세스하는 방법 설명
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869324"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Azure Portal에서 CloudSimple 포털로 VMware 솔루션에 액세스 합니다.

Single sign-on은 CloudSimple 포털에 대 한 액세스를 지원 합니다. Azure Portal에 로그인 한 후에 다시 로그인 하지 않고 CloudSimple 포털에 액세스할 수 있습니다. CloudSimple 포털에 처음 액세스할 때 [Cloudsimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 응용 프로그램에 권한을 부여 하 라는 메시지가 표시 됩니다.  권한 부여는 일회성 작업입니다.

## <a name="before-you-begin"></a>시작하기 전에

Builtin **소유자** 및 **참여자** 역할을 가진 사용자는 cloudsimple 포털에 액세스할 수 있습니다.  CloudSimple service가 배포 되는 리소스 그룹에 역할을 구성 해야 합니다.  CloudSimple service 개체에도 역할을 구성할 수 있습니다.  역할을 확인 하는 방법에 대 한 자세한 내용은 [역할 할당 보기](https://docs.microsoft.com/azure/role-based-access-control/check-access) 문서를 참조 하세요. 기본 제공 **소유자** 및 **참가자** 역할을 가진 사용자만 cloudsimple 포털에 액세스할 수 있습니다.  구독에서 역할을 구성 해야 합니다.  역할을 확인 하는 방법에 대 한 자세한 내용은 [역할 할당 보기](https://docs.microsoft.com/azure/role-based-access-control/check-access) 문서를 참조 하세요.

사용자 지정 역할을 사용 하는 경우 역할은에서 ```Actions```다음 작업 중 하나를 수행 해야 합니다.  사용자 지정 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)을 참조 하세요.  작업이의 일부인 ```NotActions```경우 사용자는 cloudsimple 포털에 액세스할 수 없습니다.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스**를 선택합니다.

2. **Cloudsimple 서비스**를 검색 합니다.

3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.

4. **개요** 페이지에서 **Cloudsimple 포털로 이동**을 클릭 합니다.  Azure Portal에서 CloudSimple 포털에 처음 액세스 하는 경우 [Cloudsimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 응용 프로그램에 권한을 부여 하 라는 메시지가 표시 됩니다. 

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Azure Portal에서 직접 사설 클라우드 작업 (예: 사설 클라우드 만들기 또는 확장)을 선택 하는 경우 CloudSimple 포털이 표시 된 페이지에 열립니다.

CloudSimple 포털의 측면 메뉴에서 **홈** 을 선택 하 여 사설 클라우드에 대 한 요약 정보를 표시 합니다. 주의가 필요한 경고 및 작업과 함께 사설 클라우드의 리소스 및 용량이 표시 됩니다. 일반 작업의 경우 페이지 맨 위에 있는 명명 된 아이콘을 클릭 합니다.

![홈 페이지](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>CloudSimple 서비스 권한 부여 응용 프로그램에 동의

Azure Portal 처음으로 CloudSimple 포털을 시작 하려면 CloudSimple 서비스 권한 부여 응용 프로그램에 대 한 동의가 필요 합니다.  **수락** 을 선택 하 여 요청 된 권한을 부여 하 고 cloudsimple 포털에 액세스 합니다.

![CloudSimple 서비스 권한 부여에 대 한 동의-관리자](media/cloudsimple-azure-consent.png)

전역 관리자 권한이 있는 경우 조직에 동의할 수 있습니다.  **조직을 대신하여 동의**를 선택합니다.

![CloudSimple 서비스 권한 부여에 동의-전역 관리자](media/cloudsimple-azure-consent-global-admin.png)

사용 권한이 CloudSimple 포털에 대 한 액세스를 허용 하지 않으면 테 넌 트의 전역 관리자에 게 문의 하 여 필요한 권한을 부여 합니다.  전역 관리자는 조직을 대신 하 여 동의할 수 있습니다.

![CloudSimple 서비스 권한 부여에 동의-관리자 필요](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>다음 단계

* [사설 클라우드를 만드는](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/) 방법 알아보기
* [사설 클라우드 환경을 구성](quickstart-create-private-cloud.md) 하는 방법 알아보기
