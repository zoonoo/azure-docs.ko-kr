---
title: QnAMaker 문제 해결 | Microsoft Docs
titleSuffix: Azure
description: QnAMaker 런타임 문제를 해결하는 방법
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "35383232"
---
# <a name="qnamaker-troubleshooting"></a>QnAMaker 문제 해결
QnAMaker는 사용자의 Azure 계정에 호스트된 구성 요소로 구성됩니다. 디버깅 과정에서 사용자가 QnAMaker Azure 리소스를 조작하거나 QnAMaker 지원 팀에 설치에 대한 추가 정보를 제공해야 할 수도 있습니다.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>최신 QnAMaker 런타임 업데이트를 얻는 방법
QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들 때](./set-up-qnamaker-service-azure.md) 배포한 Azure App Service의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnAMaker 설치에 최신 업데이트를 적용하려면 App Service를 다시 시작해야 합니다.
1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service를 클릭하고 [개요] 섹션 열기

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. App Service를 다시 시작합니다. 몇 초 이내에 완료될 것입니다. 다시 시작되는 시간에는 이 QnAMaker 서비스의 다운스트림 응용 프로그램/봇 빌드를 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker appservice 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnAMaker 서비스 호스트 이름을 가져오는 방법
QnAMaker 서비스 호스트 이름은 QnAMaker 고객 지원팀 또는 UserVoice에 디버깅에 대해 문의할 때 유용합니다. 호스트 이름은 https://*{hostname}*.azurewebsites.net 형식의 URL입니다.
    
1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service 클릭

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. 호스트 이름 URL은 [개요] 섹션에서 사용 가능

    ![QnAMaker 호스트 이름](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnAMaker API 사용](./upgrade-qnamaker-service.md)
