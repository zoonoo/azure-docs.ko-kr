---
title: 사용자 지정 CA 인증서 추가 - Azure API Management | Microsoft Docs
description: Azure API Management에서 사용자 지정 CA 인증서를 추가하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 5161a35fd52b2f3d8374c76bdab60281e33dacf6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730650"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management에서 사용자 지정 CA 인증서를 추가하는 방법

Azure API Management를 통해 신뢰할 수 있는 루트 및 중간 인증서 저장소 내의 머신에 CA 인증서를 설치할 수 있습니다. 서비스에 사용자 지정 CA 인증서가 필요한 경우 이 기능을 사용해야 합니다.

이 문서에서는 Azure Portal에서 Azure API Management 서비스 인스턴스의 CA 인증서를 관리하는 방법을 보여줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>CA 인증서 업로드

![CA 인증서 추가](media/api-management-howto-ca-certificates/00.png)

새 CA 인증서를 업로드하려면 다음 단계를 수행합니다. 아직 API Management 서비스 인스턴스를 만들지 않은 경우 자습서 [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

1. Azure Portal에서 Azure API Management 서비스 인스턴스로 이동합니다.

2. 메뉴에서 **CA 인증서**를 선택합니다.

3. **+추가** 단추를 클릭합니다.  

    ![CA 인증서 추가](media/api-management-howto-ca-certificates/01.png)  

4. 인증서를 찾고 인증서 저장소를 결정합니다. 공개 키만 필요하므로 암호는 필요하지 않습니다.

    ![CA 인증서 추가](media/api-management-howto-ca-certificates/02.png)  

5. **저장**을 클릭합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    ![CA 인증서 추가](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> `New-AzApiManagementSystemCertificate` Powershell 명령을 사용하여 CA 인증서를 업로드할 수 있습니다.

## <a name="step1a"> </a>클라이언트 인증서 삭제

인증서를 삭제하려면 바로 가기 메뉴 **...** 를 클릭하고 인증서 옆에 있는 **삭제**를 선택합니다.

![CA 인증서 삭제](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
