---
title: .NET SDK를 사용하여 Azure Mobile Engagement 서비스 API에 액세스
description: Mobile Engagement .NET SDK를 사용하여 Azure Mobile Engagement 서비스 API에 액세스하는 방법을 설명합니다.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 99595eb1f9a1eab1db51796632d58df35bf45be6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>.NET SDK를 사용하여 Azure Mobile Engagement 서비스 API에 액세스
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

장치, 도달률/푸시 캠페인 등을 관리하기 위해 Azure Mobile Engagement는 API 집합을 노출합니다. 또한 이러한 API와 상호 작용하기 위해 기본 설정 언어에 대한 SDK를 생성하는 도구와 함께 사용할 수 있는 [Swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)을 제공합니다. Swagger 파일에서 SDK를 생성하는 [AutoRest](https://github.com/Azure/AutoRest) 도구를 사용하는 것이 좋습니다.

> [!NOTE]
> Azure Mobile Engagement 서비스는 2018년 3월에 사용 중지되며 현재 기존 고객에게만 제공됩니다. 자세한 내용은 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)를 참조하세요.

C# 래퍼를 사용하여 이러한 API와 상호 작용할 수 있는 유사한 방식으로 .NET SDK를 만들었으며 인증 토큰 협상을 수행하거나 새로 고칠 필요가 없습니다.  

이 샘플에서는 .NET SDK를 사용하기 위해 일련의 단계를 수행합니다.

1. 먼저 [여기](mobile-engagement-api-authentication.md#authentication)설명된 대로 Azure Active Directory를 사용하여 API에 대한 인증을 설정해야 합니다. 마지막 단계에서는 유효한 **SubscriptionId**, **TenantId**, **ApplicationId** 및 **암호**가 필요합니다. 
2. 간단한 Windows 콘솔 앱을 사용하여 알림 캠페인을 만드는 시나리오에서 .NET SDK를 사용하는 작업을 보여 줍니다. 따라서 Visual Studio를 열고 **콘솔 응용 프로그램**을 만듭니다.   
3. 다음으로 **여기** Nuget 갤러리에서 [Microsoft Azure Engagement 관리 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/)로 사용할 수 있는 .NET SDK를 다운로드해야 합니다.
   Visual Studio에서 Nuget을 설치하는 경우 패키지를 검색하는 동안 **시험판 포함** 옵션을 선택했는지 확인해야 합니다.
   
    ![][1]
4. `Program.cs` 파일에서 다음 네임스페이스를 추가합니다.
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. 다음으로 알림 캠페인을 만들 Mobile Engagement 앱을 인증하고 이 앱과 상호 작용하는 데 사용할 다음 상수를 정의해야 합니다.
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Mobile Engagement SDK 메서드를 호출하는 데 사용할 `EngagementManagementClient` 변수를 정의합니다.
   
        static EngagementManagementClient engagementClient; 
7. `Main` 메서드에 다음을 추가합니다.
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. 먼저 인증한 다음 알림 캠페인을 만들려는 Mobile Engagement 앱과 연결하여 `EngagementManagementClient` 를 초기화하는 다음 메서드를 정의합니다.
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > AppName 매개 변수에 대해 Azure 관리 포털에 정의된 **앱 리소스 이름**을 사용해야 합니다. 
   > 
   > 
9. 마지막으로 제목 및 메시지로 간단한 **항상** & **알림 전용** 캠페인을 만들기 위해 이전에 초기화된 EngagementClient를 사용하는 CreateCampaign 메서드를 정의합니다. 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. 콘솔 앱을 실행하면 캠페인이 성공적으로 만들어지고 다음이 표시됩니다.
    
    **캠페인 ID '159'가 성공적으로 만들어졌으며 '초안' 상태입니다**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
