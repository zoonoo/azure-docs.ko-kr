<properties 
	pageTitle="앱 서비스 환경에 대한 사용자 지정 설정" 
	description="앱 서비스 환경에 대한 사용자 지정 구성 설정" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="stefsch"/>

# 앱 서비스 환경에 대한 사용자 지정 구성 설정

## 개요 ##
앱 서비스 환경은 단일 고객에게 격리되므로 앱 서비스 환경에만 적용할 수 있는 특정 구성 설정이 있습니다. 이 문서에서는 사용 가능한 다양한 앱 서비스 환경 관련 사용자 지정 항목을 설명합니다.

앱 서비스 환경 사용자 지정 항목은 *hostingEnvironments* ARM 엔터티의 "Properties" 사전에 있는 새 특성 "clusterSettings"의 배열을 사용하여 저장됩니다.

아래의 축약된 ARM 템플릿 조각은 "clusterSettings" 특성을 보여 줍니다.


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

"clusterSettings" 특성을 ARM 템플릿에 포함하여 앱 서비스 환경을 업데이트할 수 있습니다.

또는 [Azure 리소스 탐색기](https://resources.azure.com)를 사용하여 특성 값을 업데이트할 수 있습니다. Azure 리소스 탐색기에서 앱 서비스 환경에 대한 노드로 이동(구독 --> resourceGroups --> 공급자 Micrososft.Web --> hostingEnvironments)하여 업데이트할 특정 앱 서비스 환경을 클릭합니다.

오른쪽 브라우저 창의 위쪽 도구 모음에서 "읽기/쓰기"를 클릭하여 리소스 탐색기에서 대화형 편집을 허용합니다. 그런 다음 파란색 "편집" 단추를 클릭하여 ARM 템플릿을 편집 가능하도록 설정합니다. 오른쪽 브라우저 창의 아래쪽으로 스크롤합니다. "clusterSettings" 특성은 거의 맨 아래쪽에 있습니다. 여기에서 해당 값을 입력하거나 업데이트할 수 있습니다.

"clusterSettings" 특성에서 원하는 구성 값 배열을 입력하거나 복사하여 붙여 넣습니다. 그런 다음 오른쪽 브라우저 창의 위쪽에 있는 녹색 "PUT" 단추를 클릭하여 앱 서비스 환경에 대한 0변경 내용을 커밋합니다.

앱 서비스 환경을 업데이트하기 위해 적용한 접근 방식에 상관없이 변경 내용이 제출되면 앱 서비스 환경의 프런트 엔드 수에 약 30분을 곱한 시간이 지난 후 변경 내용이 적용됩니다. 예를 들어 앱 서비스 환경에 4개의 프런트 엔드가 있는 경우 구성 업데이트를 완료하는 데 약 2시간이 걸립니다. 구성 변경이 롤아웃되는 동안 앱 서비스 환경에서 다른 크기 조정 작업 또는 구성 변경 작업을 수행할 수 없습니다.

## TLS 1.0을 사용하지 않도록 설정 ##
고객, 특히 PCI 규정 준수 감사를 다루는 고객으로부터의 되풀이되는 질문은 자신의 앱에 TLS 1.0을 명시적으로 사용하지 않도록 설정하는 기능입니다.

다음 *clusterSettings* 항목을 사용하여 TLS 1.0을 사용하지 않도록 설정할 수 있습니다.

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## 시작
Azure 빠른 시작 ARM 템플릿 사이트에는 [앱 서비스 환경 만들기](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)에 대한 기본 정의에 템플릿이 포함되어 있습니다.


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->