---
title: CloudSimple-서비스에서 Azure VMware 솔루션 만들기
description: Azure portal에서 CloudSimple 서비스를 만드는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676955"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>CloudSimple-서비스에서 Azure VMware 솔루션 만들기

시작 하려면 Azure VMware 솔루션과 CloudSimple 여 CloudSimple 서비스는 Azure portal에서 Azure VMware 솔루션을 만듭니다.

> [!NOTE]
> CloudSimple 서비스를 만들기 전에 Azure 구독에서 Microsoft.VMwareCloudSimple 리소스 공급자를 등록 해야 합니다. 단계를 따릅니다 [Azure 구독에서 Microsoft.VMwareCloudSimple 리소스 공급자를 사용 하도록 설정](enable-cloudsimple-service.md)합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스**를 선택합니다.

2. 검색할 **CloudSimple 서비스**합니다.

    ![CloudSimple 서비스 검색](media/create-cloudsimple-service-search.png)

3. 선택 **CloudSimple 서비스**합니다.

4. 클릭 **추가** 새 서비스를 만듭니다.

    ![CloudSimple 서비스 추가](media/create-cloudsimple-service-add.png)

5. CloudSimple를 만들려는 구독을 선택 합니다.

6. 서비스에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 클릭 **새로 만들기**합니다.

7. 서비스를 식별 하는 이름을 입력 합니다.

8. 서비스 게이트웨이 CIDR을 입력 합니다. / 28 지정 기존 서브넷 중 하나를 사용 하 여 겹치지 않는 서브넷입니다.  여기에 온-프레미스 서브넷에 Azure 서브넷에 포함 됩니다. 또는 모든 CloudSimple 서브넷을 계획 합니다. 서비스를 만든 후에 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)

9. **확인**을 클릭합니다.

서비스 생성 되어 서비스 목록에 추가 합니다.

## <a name="next-steps"></a>다음 단계

* 자세한 방법 [사설 클라우드 만들기](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 자세한 방법 [사설 클라우드 환경 구성](quickstart-create-private-cloud.md)
