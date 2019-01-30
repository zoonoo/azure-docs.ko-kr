---
title: Azure NetApp Files에 등록 | Microsoft Docs
description: 요청을 제출하여 Azure NetApp Files 서비스에 등록하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 0a5483cd5a6316cb5d6cba3a110ca757eb2171c8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103998"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files에 등록
NetApp Azure Files를 사용하기 전에 요청을 제출하여 Azure NetApp Files 서비스에 등록해야 합니다.  등록 후에 서비스를 사용하도록 등록합니다.

## <a name="request-to-enroll-in-the-service"></a>서비스에서 등록 요청
사용자가 공개 미리 보기 프로그램에 속해 있고 Microsoft.NetApp Azure 리소스 공급자에 액세스하는 허용 목록에 지정되어야 합니다. 공개 미리 보기 프로그램에 참여하는 방법에 대한 자세한 내용은 [Azure NetApp Files 공개 미리 보기 등록 페이지](https://aka.ms/nfspublicpreview)를 참조하세요. 


## <a name="register-the-netapp-resource-provider"></a>NetApp 리소스 공급자 등록

서비스를 사용하려면 Azure NetApp Files에 Azure 리소스 공급자를 등록해야 합니다. 

1. Azure Portal의 오른쪽 위 모서리에 있는 Azure Cloud Shell 아이콘을 클릭합니다.

      ![Azure Cloud Shell 아이콘](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Azure 계정에 여러 구독이 있는 경우 Azure NetApp Files의 허용 목록에 지정된 구독을 선택합니다.
    
        az account set --subscription <subscriptionId>

3. Azure Cloud Shell 콘솔에서 구독이 허용 목록에 지정되었는지 확인하려면 다음 명령을 입력합니다.
    
        az feature list | grep NetApp

   명령 출력은 다음과 같이 나타납니다.
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>`는 구독 ID입니다.

4. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자를 등록합니다. 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` 매개 변수는 등록이 완료될 때까지 기다리도록 콘솔에 지시합니다. 등록 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

5. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자가 등록되었는지 확인합니다. 
    
        az provider show --namespace Microsoft.NetApp

  명령 출력은 다음과 같이 나타납니다.
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`는 구독 ID입니다.  `state` 매개 변수 값은 `Registered`를 나타냅니다.

6. Azure Portal에서 **구독** 블레이드를 클릭합니다.
7. 구독 블레이드에서 구독 ID를 클릭합니다. 
8. 구독 설정에서 **리소스 공급자**를 클릭하여 Microsoft.NetApp 공급자가 등록됨 상태를 나타내는지 확인합니다. 

      ![등록된 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>다음 단계  

[NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)