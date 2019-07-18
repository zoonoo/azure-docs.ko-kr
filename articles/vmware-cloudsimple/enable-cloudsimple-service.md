---
title: CloudSimple 서비스에서 Azure VMware 솔루션을 사용 하도록 설정
description: Azure 구독에서 CloudSimple 서비스를 사용 하도록 설정 하 고 다음 CLoudSimple 리소스 공급자를 등록 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154863"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Azure 구독에서 Microsoft.VMwareCloudSimple 리소스 공급자 등록

CloudSimple 서비스를 사용 하면 Azure CloudSimple VMware 솔루션을 사용할 수 있습니다. 리소스 공급자로 Microsoft.VMwareCloudSimple 서비스를 등록할 수 있습니다.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 서비스**를 선택합니다.

3. 검색 및 선택 **구독**합니다.

    ![구독 선택](media/cloudsimple-service-select-subscriptions.png)

4. CloudSimple 서비스를 사용 하도록 설정 하려는 구독을 선택 합니다.

5. 클릭 **리소스 공급자** 구독에 대 한 합니다.

6. 사용 하 여 **Microsoft.VMwareCloudSimple** 리소스 공급자를 필터링 합니다.

7. 리소스 공급자를 선택 하 고 클릭 **등록**합니다.

    ![리소스 공급자 등록](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>다음 단계

* 자세한 방법 [CloudSimple 서비스 만들기](create-cloudsimple-service.md)
* 자세한 방법 [사설 클라우드 환경 구성](quickstart-create-private-cloud.md)
