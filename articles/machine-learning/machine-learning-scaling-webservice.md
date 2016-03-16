<properties
   pageTitle="웹 서비스 확장 | Microsoft Azure"
   description="동시성을 늘리고 새 끝점을 추가하여 웹 서비스를 확장하는 방법을 알아봅니다."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="azure 기계 학습, 웹 서비스, 조작화, 확장, 끝점, 동시성"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="02/04/2016"
   ms.author="neerajkh"/>

# 웹 서비스 확장

## 동시성 늘리기

기본적으로 게시된 각각의 웹 서비스는 20개의 동시 요청을 지원하도록 구성됩니다. 아래 그림에 표시된 것처럼 [Microsoft Azure 클래식 포털](https://manage.windowsazure.com/)을 통해 이 동시성을 200개의 동시 요청으로 늘릴 수 있습니다.

[Azure 클래식 포털](https://manage.windowsazure.com/)로 이동하여 왼쪽의 기계 학습 아이콘을 클릭하고 웹 서비스 게시에 사용되는 작업 공간을 선택합니다. 원하는 웹 서비스를 클릭하고 동시성을 늘려야 하는 끝점을 선택한 다음 **구성**을 클릭합니다. 슬라이더를 사용하여 동시성을 늘린 다음 아래쪽 패널에서 **저장**을 클릭합니다.

동시성을 늘리려면 [API 끝점 확장](machine-learning-scaling-endpoints.md)을 참조하세요.

   ![기계 학습, 끝점 확장][1]

## 동일한 웹 서비스에 대한 새 끝점 추가

웹 서비스 확장은 200개 이상의 동시 요청을 지원하여 여러 끝점을 통해 가용성을 늘리거나 서로 다른 웹 서비스 소비자에 대해 별도의 끝점을 제공하는 일반적인 작업입니다. 사용자는 동일한 웹 서비스에 대해 끝점을 더 추가하여 크기를 늘릴 수 있습니다. 사용자는 아래 그림에 표시된 것처럼 [Azure 클래식 포털](https://manage.windowsazure.com/)을 통해 끝점을 더 추가할 수 있습니다.

[Azure 클래식 포털](https://manage.windowsazure.com/)로 이동하여 왼쪽의 기계 학습 아이콘을 클릭하고 웹 서비스 게시에 사용되는 작업 공간을 선택합니다. 원하는 웹 서비스를 클릭하고 아래쪽 패널에서 **끝점 추가**를 클릭한 다음 새 끝점에 대한 이름, 설명 및 원하는 동시성을 입력합니다.

새 끝점을 추가하려면 [끝점 만들기](machine-learning-create-endpoint.md)를 참조하세요.

   ![기계 학습, 새 끝점 추가][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0211_2016-->