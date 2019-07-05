---
title: 문제 해결 - QnAMaker
titlesuffix: Azure Cognitive Services
description: QnAMaker는 사용자의 Azure 계정에 호스트된 구성 요소로 구성됩니다. 디버깅 과정에서 사용자가 QnAMaker Azure 리소스를 조작하거나 QnAMaker 지원 팀에 설치에 대한 추가 정보를 제공해야 할 수도 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5b65a121e895b4855c7c69d2b67e7055c88ddd08
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466053"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>QnA Maker 서비스 및 런타임을 지원하기 위한 문제 해결 팁
QnAMaker는 사용자의 Azure 계정에 호스트된 구성 요소로 구성됩니다. 디버깅 과정에서 사용자가 QnAMaker Azure 리소스를 조작하거나 QnAMaker 지원 팀에 설치에 대한 추가 정보를 제공해야 할 수도 있습니다.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>최신 QnAMaker 런타임 업데이트를 얻는 방법
QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들 때](./set-up-qnamaker-service-azure.md) 배포한 Azure App Service의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker App Service는에서 자동 업데이트 모드 post는 2019 년 4 월 사이트 확장 릴리스 (버전 5 이상). 이 이미 업그레이드 하는 동안 가동 중지 시간이 0을 처리 하도록 설계 되었습니다. 현재 버전을 확인할 수 있습니다 https://www.qnamaker.ai/UserSettings 합니다. 버전 보다 최신 버전이 아닌 경우 5.x QnAMaker 설치에 적용 하려면 최신 업데이트를 적용 하도록 App Service를 다시 시작 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service를 클릭하고 [개요] 섹션 열기

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. App Service를 다시 시작합니다. 몇 초 이내에 완료될 것입니다. 다시 시작되는 시간에는 이 QnAMaker 서비스의 다운스트림 애플리케이션/봇 빌드를 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker appservice 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnAMaker 서비스 호스트 이름을 가져오는 방법
QnAMaker 서비스 호스트 이름은 QnAMaker 고객 지원팀 또는 UserVoice에 디버깅에 대해 문의할 때 유용합니다. 호스트 이름은 https:// *{hostname}* .azurewebsites.net 형식의 URL입니다.
    
1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![Azure Portal의 QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service 클릭

     ![QnAMaker App Service 선택](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. 호스트 이름 URL은 [개요] 섹션에서 사용 가능

    ![QnAMaker 호스트 이름](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [활성 학습을 사용하여 기술 자료 질문 개선](./improve-knowledge-base.md)
