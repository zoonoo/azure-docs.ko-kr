---
title: '자습서: 전자 상거래 카탈로그 조정 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: 기계 학습 및 AI를 사용하여 자동으로 전자 상거래 카탈로그를 조정합니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 0bd61c3f1a4f660076be4e87bb5443302e5dc013
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363997"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>자습서: 기계 학습을 사용하여 전자 상거래 카탈로그 조정

이 자습서에서는 인텔리전트 카탈로그 시스템을 제공하기 위해 기계 지원 AI 기술과 사용자 조정을 결합하여 Machine Learning 기반의 인텔리전트 전자 상거래 카탈로그 조정을 구현하는 방법에 대해 알아봅니다.

![분류된 제품 이미지](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>비즈니스 시나리오

기계 지원 기술을 사용하여 이러한 카탈로그에서 제품 이미지를 분류하고 조정합니다.

1. 성인(노출)
2. 외설(선정적)
3. 유명인
4. 미국 국기
5. 장난감
6. 펜

## <a name="tutorial-steps"></a>자습서 단계

이 자습서에서는 다음 단계를 안내합니다.

1. Content Moderator 팀에 등록하고 만듭니다.
2. 잠재적인 유명인 및 국기 콘텐츠에 대한 조정 태그(레이블)를 구성합니다.
3. Content Moderator의 이미지 API를 사용하여 잠재적인 성인 및 외설 콘텐츠를 검사합니다.
4. Computer Vision API를 사용하여 잠재적인 유명인을 검사합니다.
5. Custom Vision Service를 사용하여 국기가 있을 가능성을 검사합니다.
6. 사용자 검토 및 최종 의사 결정을 위해 뉘앙스 검사 결과를 표시합니다.

## <a name="create-a-team"></a>팀 만들기

[빠른 시작](quick-start.md) 페이지를 참조하여 Content Moderator에 등록하고 팀을 만듭니다. **자격 증명** 페이지에서 **팀 ID**를 확인합니다.


## <a name="define-custom-tags"></a>사용자 지정 태그 정의

[태그](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) 문서를 참조하여 사용자 지정 태그를 추가합니다. 기본 제공되는 **성인** 및 **외설** 태그 외에도 새로운 태그를 사용하여 검토 도구에서 태그에 대한 설명이 포함된 이름을 표시할 수 있습니다.

이 경우 다음과 같은 사용자 지정 태그(**유명인**, **국기**, **미국**, **장난감**, **펜**)를 정의합니다.

![사용자 지정 태그 구성](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>API 키 및 엔드포인트 목록

1. 자습서에서는 세 가지 API 및 해당하는 키와 API 엔드포인트를 사용합니다.
2. 사용자의 API 엔드포인트는 사용자의 구독 영역 및 Content Moderator 검토 팀 ID에 따라 다릅니다.

> [!NOTE]
> 자습서는 다음과 같은 엔드포인트에 표시된 영역에 있는 구독 키를 사용하도록 설계되었습니다. API 키가 영역 URI와 일치하는지 확인합니다. 그렇지 않으면 키에서 다음 엔드포인트를 사용할 수 없습니다.

        // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>성인 및 외설 콘텐츠 검사

1. 함수는 이미지 URL 및 키-값 쌍의 배열을 매개 변수로 사용합니다.
2. Content Moderator의 이미지 API를 호출하여 성인 및 외설 점수를 가져옵니다.
3. 값이 0.4보다 큰 경우(범위는 0에서 1 사이) **ReviewTags** 배열의 값을 **True**로 설정합니다.
4. **ReviewTags** 배열은 검토 도구에서 해당 태그를 강조 표시하는 데 사용됩니다.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>유명인 검사

1. [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/)의 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에 등록합니다.
2. **API 키 가져오기** 단추를 클릭합니다.
3. 약관에 동의합니다.
4. 로그인하려면 사용 가능한 인터넷 계정 목록에서 선택합니다.
5. 서비스 페이지에 표시되는 API 키를 참고하세요.
    
    ![Computer Vision API 키](images/tutorial-computer-vision-keys.PNG)
    
6. Computer Vision API를 사용하여 이미지를 검색하는 함수에 대한 프로젝트 소스 코드를 참조하세요.

        public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>국기, 장난감 및 펜으로 분류

1. [Custom Vision API 미리 보기](https://www.customvision.ai/)에 [로그인](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)합니다.
2. [빠른 시작](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)을 사용하여 국기, 장난감 및 펜이 있을 가능성을 감지하도록 사용자 지정 분류자를 빌드합니다.
    ![Custom Vision 학습 이미지](images/tutorial-ecommerce-custom-vision.PNG)
3. 사용자 지정 분류자에 대한 [예측 엔드포인트 URL을 가져옵니다](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api).
4. 프로젝트 소스 코드를 참조하여 이미지를 검사하도록 사용자 정의 분류자 예측 엔드포인트를 호출하는 함수를 확인합니다.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }
 
## <a name="reviews-for-human-in-the-loop"></a>사람이 참여하는 검토

1. 이전 섹션에서 들어오는 이미지를 성인 및 외설(Content Moderator), 유명인(Computer Vision) 및 국기(Computer Vision)에 대해 검사했습니다.
2. 각 검사에 대한 일치 임계값에 따라 검토 도구의 사용자 검토에 뉘앙스 케이스를 사용할 수 있습니다.

        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata)
        {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>이미지의 일괄 처리 제출

1. 이 자습서에서는 이미지 URL의 목록이 있는 텍스트 파일이 “C:Test” 디렉터리에 있다고 가정합니다.
2. 다음 코드는 파일의 존재 여부를 검사하고 메모리에 모든 URL을 읽습니다.

            // Check for a test directory for a text file with the list of Image URLs to scan
            var topdir = @"C:\test\";
            var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>모든 검색 시작

1. 이 최상위 함수는 앞에서 언급한 텍스트 파일에서 모든 이미지 URL을 통해 반복합니다.
2. 각 API로 검사하고, 일치 신뢰성 점수가 기준 내에 있으면 사용자 중재자를 위한 검토를 만듭니다.

            // for each image URL in the file...
            foreach (var Url in Urls)
            {
                // Initiatize a new review tags array
                ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>라이선스

Microsoft Cognitive Services SDK 및 샘플은 모두 MIT 라이선스를 통해 사용이 허가됩니다. 자세한 내용은 [라이선스](https://microsoft.mit-license.org/)를 참조하세요.

## <a name="developer-code-of-conduct"></a>개발자 준수 사항

이 클라이언트 라이브러리 및 샘플을 포함한 Cognitive Services를 사용하는 개발자는 http://go.microsoft.com/fwlink/?LinkId=698895에 있는 “Microsoft Cognitive Services를 위한 개발자 준수 사항”을 따라야 합니다.

## <a name="next-steps"></a>다음 단계

GitHub에서 [프로젝트 소스 파일](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)을 사용하여 자습서를 빌드 및 확장합니다.
