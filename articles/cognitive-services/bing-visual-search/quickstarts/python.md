---
title: '빠른 시작: 시각적 개체 검색 쿼리 만들기, Python - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API에 이미지를 업로드하고 이미지에 대한 중요 정보를 다시 얻는 방법을 보여줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3a0d92e42eed097e244118a60ec0a4223c9cedf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440944"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>빠른 시작: Python에서 첫 번째 Bing Visual Search 쿼리

Bing Visual Search API는 사용자가 제공하는 이미지에 대한 정보를 반환합니다. 이미지의 URL, 인사이트 토큰을 사용하거나 이미지를 업로드하여 이미지를 제공할 수 있습니다. 이러한 옵션에 대한 내용은 [Bing Visual Search API란?](../overview.md)을 참조하세요. 이 아티클에서는 이미지 업로드 방법을 보여줍니다. 이미지 업로드는 잘 알려진 이정표 사진을 찍은 후 해당 정보를 다시 얻는 모바일 시나리오에서 유용할 수 있습니다. 예를 들어, 중요 정보에는 이정표에 대한 잡학 지식이 포함될 수 있습니다. 

로컬 이미지를 업로드하는 경우 다음은 POST 본문에 포함해야 하는 양식 데이터를 보여줍니다. 양식 데이터에는 Content-Disposition 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

이 문서에는 Bing Visual Search API 요청을 보내고 JSON 검색 결과를 표시하는 간단한 콘솔 응용 프로그램이 포함되어 있습니다. Python에서 이 응용 프로그램이 작성되는 반면 API는 RESTful 웹 서비스로서 HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다. 

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Python 3](https://www.python.org/)가 필요합니다.

이 빠른 시작의 경우 [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/)에 표시된 대로 S9 가격 계층에서 구독을 시작해야 합니다. 

Azure Portal에서 구독을 시작하려면
1. `Search resources, services, and docs`라고 표시되는 Azure Portal의 맨 위에 있는 텍스트 상자에 'BingSearchV7'을 입력합니다.  
2. 드롭다운 목록의 Marketplace 아래에서 `Bing Search v7`을 선택합니다.
3. 새 리소스에 대해 `Name`을 입력합니다.
4. `Pay-As-You-Go` 구독을 선택합니다.
5. `S9` 가격 책정 계층을 선택합니다.
6. `Enable`을 클릭하여 구독을 시작합니다.

## <a name="running-the-walkthrough"></a>연습 실행

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. 즐겨 찾는 IDE 또는 편집기에서 새 Python 프로젝트를 만듭니다.
2. visualsearch.py라는 파일을 만들고, 이 빠른 시작에 표시되는 코드를 추가합니다.
3. `SUBSCRIPTION_KEY` 값을 구독 키로 바꿉니다.
3. `imagePath` 값을 업로드할 이미지의 경로로 바꿉니다.
4. 프로그램을 실행합니다.



다음에서는 Python에서 여러 부분으로 구성된 양식 데이터를 사용하여 메시지를 보내는 방법을 보여 줍니다.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>다음 단계

[인사이트 토큰을 사용하여 이미지에 대한 중요 정보 얻기](../use-insights-token.md)  
[Bing Visual Search 이미지 업로드 자습서](../tutorial-visual-search-image-upload.md)
[Bing Visual Search 단일 페이지 앱 자습서](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search 개요](../overview.md)  
[사용해보기](https://aka.ms/bingvisualsearchtryforfree)  
[평가판 액세스 키 받기](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API 참조](https://aka.ms/bingvisualsearchreferencedoc)
