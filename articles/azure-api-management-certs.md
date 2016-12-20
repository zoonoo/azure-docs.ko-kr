---
title: "Azure 관리 API 인증서 업로드 | Microsoft Docs"
description: "Azure 클래식 포털에 대한 관리 API 인증서를 업로드하는 방법에 대해 알아봅니다."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: f7b0c3acea5bef6017230df82c57ab7a6ebccd80
ms.openlocfilehash: 19e5ef3d18deb4bf49699df6449d1cafab92ff53


---
# <a name="upload-an-azure-management-api-management-certificate"></a>Azure 관리 API 관리 인증서 업로드
관리 인증서를 사용하면 Azure에서 제공하는 서비스 관리 API를 사용한 인증이 가능합니다. 많은 프로그램과 도구(예: Visual Studio 또는 Azure SDK)에서 인증서를 사용하여 다양한 Azure 서비스의 구성 및 배포를 자동화합니다. **이는 Azure 클래식 포털에만 적용됩니다**.

> [!WARNING]
> 주의가 필요합니다! 이런 종류의 인증서를 사용하면 인증서로 인증하는 사람 누구나 연관된 구독을 관리할 수 있습니다.
>
>

Azure 인증서(자체 서명 인증서 만들기를 비롯한)에 대한 자세한 정보는 필요한 경우 [사용](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) 할 수 있습니다.

[Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/)를 사용하여 자동화를 위한 클라이언트 코드를 인증할 수도 있습니다.

## <a name="upload-a-management-certificate"></a>관리 인증서 업로드
관리 인증서(공개 키만 포함하는 .cer 파일)를 만든 후에는 포털에 업로드할 수 있습니다. 포털에서 인증서를 사용할 수 있는 경우 일치하는 인증서(개인 키)가 있는 모든 사람이 관리 API를 통해 연결할 수 있고 연결된 구독에 대한 리소스에 액세스할 수 있습니다.

1. [Azure 클래식 포털](http://manage.windowsazure.com)에 로그인합니다.
2. 인증서와 연결하려는 정확한 구독을 선택해야 합니다. 포털의 오른쪽 상단에서 **구독** 텍스트를 누릅니다.

    ![설정](./media/azure-api-management-certs/subscription.png)
3. 정확한 구독을 선택한 후에 포털의 왼쪽에서 **설정** 을 누릅니다(아래로 스크롤해야 함).

    ![설정](./media/azure-api-management-certs/settings.png)
4. **관리 인증서** 탭을 누릅니다.

    ![설정](./media/azure-api-management-certs/certificates-tab.png)
5. **업로드** 단추를 누릅니다.

    ![설정](./media/azure-api-management-certs/upload.png)
6. 대화 상자 정보를 입력하고 완료 **확인 표시**를 누릅니다.

    ![설정](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>다음 단계
이제 구독과 연관된 관리 인증서가 있으므로(로컬에서 일치하는 인증서를 설치한 후에) [서비스 관리 REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) 에 프로그래밍 방식으로 연결할 수 있고 구독과 연관된 다양한 Azure 리소스를 자동화할 수 있습니다.



<!--HONumber=Nov16_HO3-->


