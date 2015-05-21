<properties urlDisplayName="Blitline Image Processing Service" pageTitle="이미지 처리에 Blitline을 사용하는 방법 - Azure 기능 가이드" metaKeywords="" description="Azure 응용 프로그램 내에서 Blitline 서비스를 사용하여 이미지를 처리하는 방법에 대해 알아봅니다." metaCanonical="" services="" documentationCenter=".NET" title="How to use Blitline with Azure and Azure Storage" authors="jason@blitline.com" solutions="" manager="jason@blitline.com" editor="jason@blitline.com" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="support@blitline.com" />






# Azure 및 Azure 저장소로 Blitline을 사용하는 방법

이 가이드는 Blitline 서비스를 액세스하는 방법 및 작업을 Blitline에 전송하는 방법을 설명합니다.

## 목차

[Blitline 정의][]
[Blitline이 수행할 수 없는 작업][]
[Blitline 계정 만들기][]
[Blitline 작업을 만드는 방법][]
[Azure 저장소에 이미지를 저장하는 방법][]
[다음 단계][]

## <a id="whatis"></a>Blitline 정의

Blitline은 직접 빌드하는 비용보다 훨씬 저렴한 가격으로 엔터프라이즈 수준의 이미지 처리를 제공하는 클라우드 기반 이미지 처리 서비스입니다.

사실 이미지 처리는 반복적으로 이루어지며 보통 각 웹 사이트 및 모든 웹 사이트에 대해 완전히 다시 빌드됩니다. 이미지를 수백만 번 다시 빌드했기 때문에 이러한 사실을 알 수 있었습니다. 그러다 문득 모든 사용자를 위해 이 작업을 수행하기로 했습니다. 작업하는 방법 및 빠르고 효율적으로 하는 방법을 알고 있으며 동시에 모든 작업을 저장합니다.

자세한 내용은 [http://www.blitline.com](http://www.blitline.com)(영문)을 참조하세요.

## <a id="whatisnot"></a>Blitline이 수행할 수 없는 작업

계속 진행하기 전에 Blitline이 수행할 수 없는 작업을 확인하는 것이 Blitline이 유용한 이유를 명확하게 하는 데 더 쉽습니다.

- Blitline에는 이미지를 업로드할 HTML 위젯이 없습니다. 공개적으로 사용할 수 있는 이미지를 가지고 있거나 Blitline가 접근할 수 있는 제한된 권한을 가진 이미지를 가지고 있어야 합니다.

- Blitline은 Aviary.com처럼 라이브로 이미지를 처리하지 않습니다.

- Blitline은 이미지 업로드를 허용하지 않아 이미지를 Blitline에 직접 푸시할 수 없습니다. 이미지를 Azure 저장소에 푸시하거나 Blitline이 지원하는 다른 위치에 푸시한 다음 Blitline에 그 위치를 알려주어 가져올 수 있게 해야 합니다.

- Blitline은 대량 병렬식이어서 동기식 처리를 하지 않습니다. 즉, postback_url을 보내주어야 처리가 완료되는 시점을 알려줄 수 있습니다.

## <a id="createaccount"></a>Blitline 계정 만들기

[WACOM.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a id="createjob"></a>Blitline 작업을 만드는 방법

Blitline은 JSON을 사용하여 이미지에 적용할 동작을 정의합니다. 이 JSON은 간단한 필드 몇 개로 구성됩니다.

다음은 가장 간단한 예제입니다.

	    json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

이 JSON에서는 "src" 이미지 "...boys.jpeg"을 사용한 다음 그 이미지 크기를 240x140으로 다시 조정합니다.

응용 프로그램 ID는 Azure의 **연결 정보** 또는 **관리** 탭에서 찾을 수 있는 ID입니다. Blitline에서 작업을 실행할 수 있도록 해주는 비밀 식별자입니다.

"save" 매개 변수는 처리한 이미지를 저장할 위치에 대한 정보를 식별합니다. 간단한 이 경우에서는 정의하지 않았습니다. 위치를 정의하지 않으면 Blitline은 고유 클라우드 위치에 이미지를 로컬로(및 일시적으로) 저장합니다. Blitline을 만들 때 Blitline이 반환한 JSON에서 그 위치를 가져올 수 있습니다. "image" 식별자는 필수이며 저장된 이 특정 이미지를 식별할 때 반환됩니다.

여기서 지원하는 *함수*에 대한 자세한 정보는 다음에서 찾을 수 있습니다. <http://www.blitline.com/docs/functions>

또한 여기서 나온 작업 옵션에 대한 설명서는 다음에서 찾을 수 있습니다. <http://www.blitline.com/docs/api>

JSON이 있는 경우 'http://api.blitline.com/jobs'에 **게시**하면 됩니다.

다음과 유사한 JSON이 반환됩니다.

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


이는 Blitline에서 요청을 받았음을 나타내고 그 요청을 처리 큐에 넣은 후 완료되면 다음에서 이미지를 사용할 수 있습니다.
**https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a id="saveazure"></a>Azure 저장소 계정에 이미지를 저장하는 방법

Azure 저장소 계정이 있으면 Blitline이 처리된 이미지를 쉽게 Azure 컨테이너에 푸시하도록 할 수 있습니다. "azure_destination"을 추가하여 푸시할 Blitline의 위치와 권한을 정의합니다.

다음은 예제입니다.

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


CAPITALIZED 값을 자체 값으로 채우면 이 JSON을 http://api.blitline.com/job에 제출할 수 있으며 "src" 이미지가 흐리게 하는 필터로 처리된 다음 Azure 대상에 푸시됩니다.

<h3>참고:</h3>

SAS에는 대상 파일의 파일 이름을 포함하여 전체 SAS URL이 포함되어야 합니다.

예제:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Blitline의 Azure 저장소 문서의 최신 버전을 [여기서](http://www.blitline.com/docs/azure_storage) 읽을 수도 있습니다.


## <a id="nextsteps"></a>다음 단계

다른 모든 기능에 대한 내용을 보려면 다음 blitline.com을 방문하십시오.

* Blitline API 끝점 문서 <http://www.blitline.com/docs/api>(영문)
* Blitline API 함수 <http://www.blitline.com/docs/functions>(영문)
* Blitline API 예제 <http://www.blitline.com/docs/examples>(영문)
* 타사 Nuget 라이브러리 <http://nuget.org/packages/Blitline.Net>(영문)


  [다음 단계]: #nextsteps
  [Blitline 정의]: #whatis
  [Blitline이 수행할 수 없는 작업]: #whatisnot
  [Blitline 계정 만들기]: #createaccount
  [Blitline 작업을 만드는 방법]: #createjob
  [Azure 저장소에 이미지를 저장하는 방법]: #saveazure


<!--HONumber=35.2-->
