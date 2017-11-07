---
title: "명명된 인증 자격 증명 설정 | Microsoft Docs"
description: "응용 프로그램을 Visual Studio에서 Azure로 게시하거나 기존 클라우드 서비스를 모니터링할 수 있도록 Visual Studio가 Azure에 대한 요청을 인증하는 데 사용할 수 있는 자격 증명을 제공하는 방법에 대해 알아봅니다."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-named-authentication-credentials"></a>명명된 인증 자격 증명 설정
Visual Studio에서 Azure에 응용 프로그램을 게시하거나 기존 클라우드 서비스를 모니터링하려면 Visual Studio에서 Azure에 대한 요청을 인증하는 데 사용할 수 있는 자격 증명을 제공해야 합니다. 이러한 자격 증명을 제공하기 위해 로그인 할 수 있는 Visual Studio의 여러 위치 사항이 있습니다. 예를 들어 서버 탐색기에서 **Azure** 노드에 대한 바로 가기 메뉴를 열고 **Microsoft Azure 구독에 연결**을 선택할 수 있습니다. 로그인할 때 Azure 계정에 연결된 구독 정보를 Visual Studio에서 사용할 수 있습니다. 아무 작업도 수행할 필요가 없습니다.

또한 Azure 도구는 구독 파일(.publishsettings 파일)을 사용하여 자격 증명을 제공하는 이전 방법을 지원합니다. 이 아티클에서는 Azure SDK 2.2에서 여전히 지원되는 이 메서드를 설명합니다.

Azure에서 인증을 받으려면 다음 항목이 필요합니다.

* 구독 ID
* 유효한 X.509 v3 인증서

> [!NOTE]
> X.509 v3 인증서 키의 길이는 2,048비트 이상이어야 합니다. Azure는 이 요구 사항을 충족하지 않거나 올바르지 않은 모든 인증서를 거부합니다.
>
>

Visual Studio는 자격 증명으로 인증서 데이터와 함께 구독 ID를 사용합니다. 적절한 자격 증명은 인증서에 대한 공용 키가 포함된 구독 파일(.publishsettings 파일)에서 참조됩니다. 구독 파일에는 둘 이상의 구독에 대한 자격 증명이 포함될 수 있습니다.

이 아티클의 뒷부분에 설명된 대로 **새 구독** 또는 **구독 편집** 대화 상자에서 구독 정보를 편집할 수 있습니다.

인증서를 직접 만들려면 [Azure용 관리 인증서 만들기 및 업로드](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)의 지침을 참조한 다음 인증서를 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 수동으로 업로드합니다.

> [!NOTE]
> Visual Studio에서 클라우드 서비스를 관리해야 하는 이러한 자격 증명은 Azure 저장소 서비스에 대한 요청을 인증하는데 필요한 자격 증명과 동일하지 않습니다.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Visual Studio에서 인증 자격 증명 가져오기, 설정 또는 편집

1. 서버 탐색기에서 **Azure** 노드에 대한 바로 가기 메뉴를 열고 **구독 관리 및 필터링**을 선택합니다.
2. **인증서** 탭을 선택한 다음, 다음 방법 중 하나를 사용합니다.

    * **가져오기**를 선택하여 **Microsoft Azure 구독 가져오기** 대화 상자를 엽니다. 현재 로드된 구독에 대한 구독 파일을 다운로드하고, 다운로드된 위치를 찾은 다음, 인증에 사용할 수 있게 가져올 수 있습니다.
    * **새로 만들기**를 선택하여 **새 구독** 대화 상자를 엽니다. 인증에서 사용하기 위해 새 구독을 설정할 수 있습니다.
    * **편집**(활성 구독을 선택한 후)을 선택하여 **구독 편집** 대화 상자를 엽니다. 인증에 사용하기 위해 기존 구독을 편집할 수 있습니다. 

다음 절차는 **새 구독** 대화 상자가 열려 있다고 가정합니다.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Visual Studio에서 인증 자격 증명 설정
1. **인증에 대해 기존 인증서 선택** 목록에서 인증서를 선택합니다.
2. **전체 경로 복사** 링크를 선택합니다. 인증서(.cer 파일)에 대한 경로가 클립보드에 복사됩니다.

   > [!IMPORTANT]
   > Visual Studio에서 Azure 응용 프로그램을 게시하려면 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 이 인증서를 업로드해야 합니다.
   >
   >
3. Azure Portal에 인증서를 업로드하려면:

   a. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)을 엽니다.
   
   b. 메시지가 표시되면 포털에 로그인한 다음 **설정** > **인증서 관리**로 이동합니다.
   
   c. **인증서 관리** 창에서 **업로드**를 선택합니다.
   
   d. Azure 구독을 선택하고 방금 만든 .cer 파일의 전체 경로를 붙여넣은 후 **업로드**를 선택합니다.

## <a name="next-steps"></a>다음 단계
* [웹앱의 일반적인 개요](https://docs.microsoft.com/azure/app-service/)
* [Azure App Service에 앱 배포](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [Visual Studio를 사용하여 WebJob 배포](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [클라우드 서비스 만들기 및 배포](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)