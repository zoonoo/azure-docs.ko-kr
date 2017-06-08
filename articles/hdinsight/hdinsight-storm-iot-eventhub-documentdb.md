---
title: "HDInsight의 Apache Storm을 사용하여 차량 센서 데이터 처리 | Microsoft Docs"
description: "HDInsight의 Apache Storm을 사용하여 이벤트 허브에서 차량 센서 데이터를 처리하는 방법에 대해 알아봅니다. Azure Cosmos DB에서 모델 데이터를 추가하고 저장소에 출력을 저장합니다."
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 차량 센서 데이터를 처리하는 방법에 대해 알아봅니다.

HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 차량 센서 데이터를 처리하는 방법에 대해 알아봅니다. 이 예제에서는 Azure Event Hub에서 센서 데이터를 읽고, Azure Cosmos DB에 저장된 데이터를 참조하여 데이터를 보강합니다. 데이터는 HDFS(Hadoop 파일 시스템)를 사용하여 Azure Storage에 저장됩니다.

![HDInsight 및 IoT(사물 인터넷) 아키텍처 다이어그램](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>개요

차량에 센서를 추가하면 과거 데이터 추세를 기반으로 장비 문제를 예측할 수 있습니다. 또한 사용 패턴 분석에 따라 향후 버전을 개선할 수 있습니다. MapReduce 처리가 가능하기 위해서는 모든 차량의 데이터를 Hadoop으로 빠르고 효율적으로 로드할 수 있어야 합니다. 또한 중요한 고장 경로(엔진 온도, 브레이크 등)에 대한 분석을 실시간으로 수행할 수 있습니다.

Azure Event Hub는 센서에서 생성된 방대한 데이터 볼륨을 처리하기 위해 개발되었습니다. Apache Storm은 HDFS에 데이터를 저장하기 전에 데이터를 로드해 처리하는 데 사용할 수 있습니다.

## <a name="solution"></a>해결 방법

엔진 온도, 주변 온도 및 차량 속도에 대한 원격 분석 데이터는 센서에서 기록합니다. 그런 다음 데이터는 VIN(차량 식별 번호) 및 타임스탬프와 함께 Event Hub로 전송됩니다. 그러면 HDInsight의 Apache Storm에서 실행되는 Storm 토폴로지가 데이터를 읽고 처리한 후 HDFS에 저장합니다.

처리하는 동안 VIN은 Cosmos DB에서 모델 정보를 검색하는 데 사용됩니다. 이 데이터는 저장되기 전에 데이터 스트림에 추가됩니다.

Storm 토폴로지에서 사용되는 구성 요소는 다음과 같습니다.

* **EventHubSpout** - Azure 이벤트 허브에서 데이터를 읽습니다.
* **TypeConversionBolt** - JSON 문자열을 Event Hub에서 다음 센서 데이터가 포함된 튜블로 변환합니다.
    * 엔진 온도
    * 주변 온도
    * 속도
    * VIN
    * Timestamp
* **DataReferencBolt** - VIN을 사용하여 Cosmos DB에서 차량 모델을 조회합니다.
* **WasbStoreBolt** - HDFS(Azure 저장소)에 데이터를 저장합니다.

다음 이미지는 이 솔루션의 다이어그램입니다.

![Storm 토폴로지](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>구현

이 시나리오에 대한 전체 자동화된 솔루션은 GitHub에서 [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) 리포지토리의 일부로 제공됩니다. 이 예제를 사용하려면 [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)의 단계를 따르세요.

## <a name="next-steps"></a>다음 단계

Storm 토폴로지에 대한 자세한 내용은 [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)를 참조하세요.


