---
title: CloudSimple-포털에서 액세스 Azure VMware 솔루션
description: Azure portal에서 CloudSimple 포털에서 VMware 솔루션을 액세스 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30882899e5be4101ae3d77f9840d8bdef567e53f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676985"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Azure portal에서 CloudSimple 포털에서 VMware 솔루션에 액세스

Single sign on CloudSimple 포털에 대 한 액세스에 지원 됩니다. Azure portal에 로그인 한 후 다시 로그인 하지 않고 CloudSimple 포털에 액세스할 수 있습니다. 권한을 부여 하 라는 메시지가 CloudSimple 포털에 액세스할 처음 합니다 [CloudSimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 응용 프로그램입니다.  권한 부여는 일회성 작업입니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스**를 선택합니다.

2. 검색할 **CloudSimple 서비스**합니다.

3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.

4. 에 **개요** 페이지에서 클릭 **CloudSimple 포털로 이동**합니다.  Azure portal에서 CloudSimple 포털에 액세스할 때는 처음으로, 메시지가 표시 됩니다 권한을 부여 하는 [CloudSimple 서비스 권한 부여](#consent-to-cloudsimple-service-authorization-application) 응용 프로그램입니다. 

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

> [!TIP]
> Azure portal에서 직접는 사설 클라우드 작업 (예: 만들기 또는 사설 클라우드 확장)를 선택 하면 CloudSimple 포털 표시 된 페이지에 열립니다.

CloudSimple 포털에서 선택 **홈** 사설 클라우드에 대 한 요약 정보를 표시 하려면 왼쪽 메뉴에 있습니다. 경고와 작업 주의가 필요한 리소스 및에 사설 클라우드 용량의 표시 됩니다. 일반적인 작업에 대 한 페이지의 맨 위에 있는 명명 된 아이콘을 클릭 합니다.

![홈 페이지](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>CloudSimple 서비스 권한 부여 응용 프로그램에 동의

Azure portal에서 CloudSimple 포털을 처음 시작 CloudSimple 서비스 권한 부여 응용 프로그램에 대 한 사용자의 동의 필요 합니다.  선택 **Accept** 요청된 권한을 부여 하 여 CloudSimple 포털에 액세스 합니다. 

![CloudSimple 서비스 권한 부여-관리자 동의](media/cloudsimple-azure-consent.png)

전역 관리자 권한이 있는 경우에 조직에 동의할 수 있습니다.  선택 **조직을 대신 하 여 동의**합니다.

![CloudSimple 서비스 권한 부여-전역 관리자 동의](media/cloudsimple-azure-consent-global-admin.png)

사용 권한을 CloudSimple 포털에 대 한 액세스를 허용 하지 필요한 권한을 부여 하려면 테 넌 트의 전역 관리자에 게 문의 합니다.  전역 관리자는 조직을 대신 하 여 동의할 수 있습니다.

![관리자-CloudSimple 서비스 권한 부여에 동의](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>다음 단계

* 자세한 방법 [사설 클라우드 만들기](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 자세한 방법 [사설 클라우드 환경 구성](quickstart-create-private-cloud.md)