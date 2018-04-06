---
title: Azure Mobile Engagement - 백 엔드 통합
description: Azure Mobile Engagement를 SharePoint 백 엔드와 연결하여 SharePoint에서 캠페인 만들기
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cf530ca6dce32050487f367aa80b10bf7f20b7e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement - API 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

자동화된 마케팅 시스템에서는 마케팅 캠페인을 만들고 활성화하는 작업도 자동으로 수행됩니다. 이를 위해 Azure Mobile Engagement에서는 API를 사용하여 이런 자동화된 마케팅 캠페인을 만들 수 있습니다. 

일반적으로 고객은 Mobile Engagement 프런트 엔드 인터페이스를 사용하여 공지/설문 조사 등을 마케팅 캠페인 부분으로 만듭니다. 그러나 마케팅 캠페인이 성숙하면서 백 엔드 시스템으로 흘러 들어가거나 나오는 데이터에 따라 동적으로 Mobile Engagement에서 캠페인을 만드는 완전 자동화된 파이프라인을 만들 수 있도록 백 엔드 시스템(예: 모든 CRM 시스템 또는 SharePoint 같은 CMS 시스템)에 잠겨 있는 데이터를 활용해야 할 필요가 있습니다. 

![][5]

이 자습서에서는 SharePoint 비즈니스 사용자가 SharePoint 목록을 마케팅 데이터로 채우고 자동화된 프로세스가 목록에서 항목을 선택하고 Mobile Engagement 시스템과 연결하여 사용 가능한 REST API를 통해 SharePoint 데이터에서 마케팅 캠페인을 만드는 시나리오를 살펴봅니다. 

> [!IMPORTANT]
> 일반적으로 이 샘플에서는 API 호출의 두 가지 주요 측면인 매개 변수 인증 및 전달에 대해 자세히 설명하므로 이 샘플을 기반으로 Mobile Engagement REST API를 호출하는 방법을 이해할 수 있습니다. 
> 
> 

## <a name="sharepoint-integration"></a>SharePoint 통합
1. 샘플 SharePoint 목록은 다음과 같습니다. **제목**, **범주**, **NotificationTitle**, **메시지** 및 **URL**은 공지를 만드는 데 사용됩니다. 콘솔 프로그램 형식으로 샘플 자동화 프로세스에서 사용되는 **IsProcessed** 라는 열이 있습니다. 이 콘솔 프로그램을 Azure WebJob으로 실행하여 예약하거나 직접 SharePoint 워크플로를 사용하여 항목이 SharePoint 목록에 삽입될 때 공지를 만들고 활성화하도록 프로그래밍할 수 있습니다. 이 샘플에서는 SharePoint 목록의 항목을 확인하고 Azure Mobile Engagement에서 각 항목에 대한 공지를 만들고 나서 마지막으로 공지를 성공적으로 만들었을 때 **IsProcessed** 플래그를 true로 표시하는 콘솔 프로그램을 사용합니다.
   
    ![][1]
2. *여기* [클라이언트 개체 모델을 사용하여 SharePoint 온라인에서 원격 인증](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) (영문)의 코드를 사용하여 SharePoint 목록으로 인증합니다.
3. 인증되고 나면 목록 항목을 반복하여 새로 만들어진 항목을 찾습니다( **IsProcessed** = false가 포함됨). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Mobile Engagement 통합
1. 처리해야 할 항목을 찾고 나서 목록 항목에서 공지를 만드는 데 필요한 정보를 추출하고 `CreateAzMECampaign`을 호출하여 공지를 만들고 나서 `ActivateAzMECampaign`을 호출하여 공지를 활성화합니다. 이들 호출은 Mobile Engagement 백 엔드를 호출하는 필수 REST API 호출입니다. 
2. Mobile Engagement REST API에는 Azure Portal에서 가져오는 `ApplicationId` 및 `ApiKey`로 구성된 **기본 인증 체계 권한 부여 HTTP 헤더**가 필요합니다. **API 키** 섹션의 키를 사용하고 **SDK 키** 섹션의 키를 사용하지 *않는지* 확인하세요. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. 공지 유형 캠페인을 만들려면 [설명서](https://msdn.microsoft.com/library/azure/mt683750.aspx)를 참조하세요. 캠페인 `kind` 를 *공지* 및 [페이로드](https://msdn.microsoft.com/library/azure/mt683751.aspx) 로 지정하고 FormUrlEncodedContent로 전달하는지 확인해야 합니다. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. 공지가 만들어지면 Mobile Engagement 포털에 다음과 같이 표시됩니다(State=Draft 및 Activated = N/A 확인).
   
    ![][3]
5. `CreateAzMECampaign` 은 공지 캠페인을 만들고 해당 ID를 호출자에 반환합니다. `ActivateAzMECampaign` 에는 캠페인을 활성화하기 위해 이 ID가 매개 변수로 필요합니다. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. 공지가 활성화되면 Mobile Engagement 포털에 다음과 같이 표시됩니다.
   
    ![][4]
7. 캠페인이 활성화되면 바로 캠페인 조건을 충족하는 모든 장치에 알림이 표시되기 시작합니다. 
8. 공지 캠페인이 만들어지고 나면 IsProcessed = false가 표시된 목록이 True로 설정되었음을 알 수 있습니다.  

이 샘플에서는 대부분 필수 속성을 지정하여 간단한 공지 캠페인을 만들었습니다. [여기](https://msdn.microsoft.com/library/azure/mt683751.aspx)에서 정보를 사용하여 포털에서 원하는 만큼 이 샘플을 사용자 지정할 수 있습니다. 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



