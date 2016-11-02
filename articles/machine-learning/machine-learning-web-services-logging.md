<properties 
    pageTitle="기계 학습 웹 서비스에 대한 로깅 | Microsoft Azure" 
    description="기계 학습 웹 서비스에 대해 로깅을 사용하도록 설정하는 방법을 알아봅니다. 로깅은 API 문제를 해결하는 데 도움이 되는 추가 정보를 제공합니다." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>


# <a name="enable-logging-for-machine-learning-web-services"></a>기계 학습 웹 서비스에 대해 로깅 사용  

이 문서에서는 기존 웹 서비스의 로깅 기능을 설명합니다. 웹 서비스에서 로깅을 사용하도록 설정하면 오류 번호 및 메시지 외에 Machine Learning API 호출 문제를 해결하는 데 유용한 추가 정보를 이용할 수 있습니다.  

Azure 클래식 포털에서 웹 서비스의 로깅을 사용하도록 설정하려면 다음을 수행합니다.   

1.  [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
2.  왼쪽 기능 열에서 **MACHINE LEARNING**을 클릭합니다.
3.  작업 영역을 클릭한 다음 **웹 서비스**를 클릭합니다.
4.  웹 서비스 목록에서 웹 서비스의 이름을 클릭합니다.
5.  끝점 목록에서 끝점 이름을 클릭합니다.
6.  **구성**을 클릭합니다.
7.  **진단 추적 수준**을 *오류* 또는 *모두*로 설정한 다음 **저장**을 클릭합니다.

Azure Machine Learning 웹 서비스 포털에서 로깅을 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Machine Learning 웹 서비스](https://services.azureml.net) 포털에 로그인합니다.
2. 기존 웹 서비스를 클릭합니다.
3.  웹 서비스 목록에서 웹 서비스의 이름을 클릭합니다.
4.  끝점 목록에서 끝점 이름을 클릭합니다.
5.  **구성**을 클릭합니다.
6.  **로깅**을 *오류* 또는 *모두*로 설정한 다음 **SAVE**를 클릭합니다.

## <a name="the-effects-of-enabling-logging"></a>로깅 활성화의 효과

로깅을 사용하도록 설정하면 선택한 끝점의 모든 진단 및 오류가 사용자 작업 영역과 연결된 Azure Storage 계정에 기록됩니다. 이 Storage 계정은 작업 영역의 Azure 클래식 포털 대시보드 보기(간략 상태 섹션의 맨 아래)에서 확인할 수 있습니다.  

Azure Storage 계정을 '탐색'하는 데 사용할 수 있는 여러 도구 중 하나를 통해 로그를 볼 수 있습니다. 가장 쉬운 방법은 Azure 클래식 포털에서 Storage 계정으로 이동한 다음 **컨테이너**를 클릭하는 것입니다. 그러면 **ml-diagnostics**라는 컨테이너가 표시됩니다. 이 컨테이너는 이 Storage 계정과 연결된 모든 작업 영역의 모든 웹 서비스 끝점에 대한 모든 진단 정보를 포함합니다. 
 
## <a name="log-blob-detail-information"></a>Blob 세부 정보 기록

컨테이너의 각 Blob은 정확히 다음 중 하나에 대한 진단 정보를 포함합니다.

-   일괄 처리 실행 메서드 실행  
-   요청-응답 메서드 실행  
-   요청-응답 컨테이너 초기화
  
각 Blob의 이름에는 다음과 같은 형식의 접두사가 있습니다. 

{작업 영역 Id}-{웹 서비스 Id}-{끝점 Id}/{로그 형식}  

로그 형식은 다음 값 중 하나를 사용합니다.  

- batch  
- score/requests  
- score/init  


<!--HONumber=Oct16_HO2-->


