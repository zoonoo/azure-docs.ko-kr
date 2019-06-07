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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676940"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Azure 구독에서 Microsoft.VMwareCloudSimple 리소스 공급자 등록

CloudSimple 서비스를 사용 하면 Azure CloudSimple VMware 솔루션을 사용할 수 있습니다. CloudSimple 서비스를 사용 하려면 먼저 설정 해야 Azure 구독에 있습니다. 다음 리소스 공급자로 Microsoft.VMwareCloudSimple 서비스를 등록할 수 있습니다.

## <a name="enable-the-cloudsimple-service"></a>CloudSimple 서비스를 사용 하도록 설정

Azure 구독에서 CloudSimple 서비스를 사용 하 여 지원 요청을 엽니다 [Microsoft 지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)합니다. 요청을 제출 하는 경우 다음 옵션을 선택 합니다.

* 문제 유형: **기술**
* 구독: **구독 ID**
* 서비스 유형: **CloudSimple VMware 솔루션**
* 문제 유형: **전용된 노드 할당량**
* 문제가 하위 유형: **전용된 노드 할당량을 늘리려면**
* 주체: **CloudSimple 서비스를 사용 하도록 설정**

Microsoft 계정 담당자에 게 문의할 수 있습니다 [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)합니다. 전자 메일에 Azure 구독 ID를 제공 합니다.  

CloudSimple 서비스 구독에 대해 활성화 된 후에 구독에서 리소스 공급자를 사용할 수 있습니다.

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