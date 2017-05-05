---
title: "HDInsight에서 Apache Storm을 사용하는 Twitter 추세 항목| Microsoft Docs"
description: "Trident를 사용하여 해시 태그에 기반하는 Twitter에서 추세 항목을 확인하는 Apache Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d588221586f151319436525c5098b0bb2694e5f9
ms.lasthandoff: 04/18/2017


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>HDInsight에서 Apache Storm을 사용하여 Twitter 추세 항목 확인

Trident를 사용하여 Twitter에서 추세 항목(해시 태그)을 확인하는 Storm 토폴로지를 만드는 방법에 대해 알아봅니다.

Trident는 조인, 집계, 그룹화, 함수 및 필터와 같은 도구를 제공하는 높은 수준의 추상화입니다. 또한 Trident는 상태 저장, 증분 처리를 수행하기 위한 기본 요소를 추가합니다. 이 문서에 사용된 예제는 사용자 지정 Spout와 함수가 포함된 Trident 토폴로지입니다. 또한 Trident에서 제공한 몇 가지 기본 함수를 사용합니다.

## <a name="requirements"></a>요구 사항

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java 및 JDK 1.8</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Twitter 개발자 계정

## <a name="download-the-project"></a>프로젝트를 다운로드합니다.

다음 코드를 사용하여 프로젝트를 로컬로 복제합니다.

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="understanding-the-topology"></a>토폴로지 이해

다음 다이어그램은 데이터가 이 토폴로지를 통해 이동하는 방식을 보여줍니다.

![토폴로지](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> 이 다이어그램은 토폴로지를 단순화한 그림입니다. 구성 요소의 여러 인스턴스가 클러스터 내 노드 사이에 배포됩니다.


토폴로지를 구현하는 Trident 코드는 다음과 같습니다.

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

이 코드는 다음 작업을 수행합니다.

1. Spout에서 스트림을 만듭니다. Spout는 Twitter에서 트윗을 검색하여 특정 키워드(이 예제의 경우 love, music 및 coffee)로 필터링합니다.

2. 사용자 지정 함수인 HashtagExtractor는 각 트윗에서 해시 태그를 추출하는 데 사용됩니다. 해시 태그는 스트림으로 보내집니다.

3. 스트림은 해시 태그로 그룹화되어 집계로 전달됩니다. 이 집계는 각 해시 태그가 발생한 횟수를 계산합니다. 이 데이터는 메모리에 유지됩니다. 마지막으로 해시 태그 및 개수가 포함된 새 스트림을 내보냅니다.

4. **FirstN** 어셈블리는 count 필드를 기준으로 상위 10개 값만 반환하도록 적용됩니다.

> [!NOTE]
> Trident 사용에 대한 자세한 내용은 [Trident API 개요](http://storm.apache.org/releases/current/Trident-API-Overview.html) 문서를 참조하세요.

### <a name="the-spout"></a>spout

**TwitterSpout** Spout는 [Twitter4j](http://twitter4j.org/en/)를 사용하여 Twitter에서 트윗을 검색합니다. __love__, **music**, **coffee** 단어에 대한 필터가 생성됩니다. 수신된 트윗(상태) 중 필터와 일치하는 필터는 연결된 차단 큐에 저장됩니다. 마지막으로 항목을 큐에서 가져와 토폴로지로 내보냅니다.

### <a name="the-hashtagextractor"></a>HashtagExtractor

해시태그를 추출하기 위해 [getHashtagEntities](http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--)를 사용하여 트윗에 포함된 모든 해시 태그를 검색합니다. 그런 다음 스트림으로 보내집니다.

## <a name="configure-twitter"></a>Twitter 구성

새 Twitter 응용 프로그램을 등록하고를 Twitter에서 읽는 데 필요한 소비자 및 액세스 토큰 정보를 받으려면 다음 단계를 참조하세요.

1. [Twitter Apps](https://apps.twitter.com)로 이동하여 **Create new app** 단추를 클릭합니다. 양식을 작성할 때 **Callback URL** 필드는 비워 둡니다.

2. 앱이 만들어지면 **Keys and Access Tokens** 탭을 클릭합니다.

3. **Consumer Key** 및 **Consumer Secret** 정보를 복사합니다.

4. 토큰이 없는 경우 페이지 아래쪽에서 **Create my access token** 을 선택합니다. 토큰을 만들었으면 **Access Token** 및 **Access Token Secret** 정보를 복사합니다.

5. 앞에서 복사한 **TwitterSpoutTopology** 프로젝트에서 **resources/twitter4j.properties** 파일을 열고 이전 단계에서 수집한 정보를 추가한 다음 파일을 저장합니다.

## <a name="build-the-topology"></a>토폴로지 작성

다음 코드를 사용하여 프로젝트를 빌드합니다.

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>포톨로지 테스트

다음 명령을 사용하여 토폴로지를 로컬로 테스트합니다.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

토폴로지가 시작된 후 해당 토폴로지에서 내보낸 해시 태그 및 개수가 포함된 디버그 정보가 표시됩니다. 출력은 다음 텍스트와 유사합니다.

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>다음 단계

토폴로지를 로컬로 테스트했으므로 이제 [HDInsight의 Apache Strom 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology.md)에서 토폴로지를 배포하는 방법에 대해 알아봅니다.

다음과 같은 Storm 항목을 참조할 수도 있습니다.

* [Maven을 사용하여 HDInsight의 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [Visual Studio를 사용하여 HDInsight의 Storm용 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)

HDinsight에 대한 추가 Storm 예제:

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)


