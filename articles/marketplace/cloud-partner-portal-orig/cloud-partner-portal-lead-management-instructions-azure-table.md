---
title: Azure 테이블 | Microsoft Docs
description: Azure 테이블 관련 잠재 고객 관리 구성 지침을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808468"
---
<a name="lead-management-instructions-for-azure-table"></a>Azure 테이블에 대한 리드 관리 지침
============================================

이 문서에서는 영업 잠재 고객 저장용 Azure 테이블을 구성하는 방법을 설명합니다. Azure 테이블을 사용하면 고객 정보를 저장하고 사용자 지정할 수 있습니다.

## <a name="to-configure-azure-table"></a>Azure 테이블을 구성하려면

1.  Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.

2.  Azure 계정이 활성화되면 [Azure     Portal](https://portal.azure.com)에 로그인합니다.
3.  Azure Portal에서 저장소 계정을 만듭니다. 다음 화면 캡처에는 저장소 계정을 만드는 방법이 나와 있습니다. 저장소 가격에 대한 자세한 내용은 [저장소 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

    ![Azure Storage 계정을 만드는 단계](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  키로 사용할 저장소 계정 연결 문자열을 복사하여 Cloud 파트너 포털의 **저장소 계정 연결 문자열** 필드에 붙여넣습니다. 연결 문자열의 예는 다음과 같습니다. `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Azure 저장소 키](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

[Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/) 또는 다른 도구를 사용하여 저장소 테이블의 데이터를 확인할 수 있습니다. Azure 테이블의 데이터를 내보낼 수도
있습니다.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(선택 사항)** Azure 테이블에서 Azure Functions를 사용하려면

잠재 고객 수신 방법을 사용자 지정하려는 경우 Azure 테이블에서 [Azure Functions](https://azure.microsoft.com/services/functions/)를 사용합니다. Azure Functions 서비스를 사용하면 잠재 고객 생성 프로세스를 자동화할 수 있습니다.

다음 단계에는 타이머를 사용하는 Azure 함수를 만드는 방법을 설명합니다. 이 함수는 5분마다 Azure 테이블에서 새 레코드를 찾은 다음 SendGrid 서비스를 사용하여 전자 메일 알림을 전송합니다.


1.  Azure 구독에서 무료 SendGrid 서비스 계정을 [만듭니다](https://portal.azure.com/#create/SendGrid.SendGrid).

    ![SendGrid 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  SendGrid API 키 만들기 
    - **관리**를 선택하여 SendGrid UI로 이동합니다.
    - **설정**, **API 키**를 차례로 선택하고 메일 보내기 -\> 모든 권한이 있는 키를 만듭니다.
    - API 키를 저장합니다.


    ![SendGrid API 키](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  호스팅 계획 옵션 “사용 계획”을 사용하여 Azure 함수 앱을 [만듭니다](https://portal.azure.com/#create/Microsoft.FunctionApp).

    ![Azure 함수 앱 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  새 함수 정의를 만듭니다.

    ![Azure 함수 정의 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  특정 시간에 함수에서 업데이트를 보내도록 하려면 시작 옵션으로 **TimerTrigger-CSharp**를 선택합니다.

     ![Azure 함수 시간 트리거 옵션](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  “개발” 코드를 다음 코드 샘플로 바꾸고 보낸 사람 및 받는 사람에 대해 사용할 주소를 사용해 전자 메일 주소를 편집합니다.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Azure 함수 코드 조각](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  **통합** 및 **입력**을 선택하여 Azure 테이블 연결을 정의합니다.

    ![Azure 함수 통합](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  테이블 이름을 입력하고 **새로 만들기**를 클릭하여 연결 문자열을 정의합니다.


    ![Azure 함수 테이블 연결](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  이제 출력을 SendGrid로 정의하고 모든 기본값을 유지합니다.

    ![SendGrid 출력](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![SendGrid 출력 기본값](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. 이름 "SendGridApiKey" 및 SendGrid UI의 API 키에서 가져온 값을 사용하여 함수 앱 설정에 SendGrid API 키를 추가합니다.

    ![SendGrid 관리](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid 키 관리](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

함수 구성을 완료한 후 통합 섹션에 표시되는 코드는 다음 예제와 같습니다.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. 마지막 단계에서는 함수의 개발 UI로 이동한 다음 **실행**을 선택하여 타이머를 시작합니다. 이제 새 잠재 고객이 수신될 때마다 알림이 표시됩니다.
