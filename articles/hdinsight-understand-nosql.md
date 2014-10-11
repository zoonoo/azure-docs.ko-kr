<properties linkid="manage-hdinsight-understand-nosql" urlDisplayName="Understand NoSQL technologies" pageTitle="Understanding NoSQL Technologies on Azure | Azure" metaKeywords="" description="Learn how NoSQL technologies on HDInsight can help you manage data not suited to relational databases, such as big data sets and JSON documents or graphs." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight" documentationCenter="" title="Data management: Understanding NoSQL technologies on Azure" authors="dchappell" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="dchappell"></tags>

# 데이터 관리: Azure의 NoSQL 기술 이해

**요약:** Azure는 NoSQL 작업에 대한 다양한 옵션을 제공하지만 자신에게 적합한 옵션이 무엇인지 쉽게 확인하지 못할 수 있습니다. 이 문서에서는 Azure의 NoSQL 기술 및 비관계형 데이터(비구조적 데이터 집합 또는 특히 보통 "빅데이터"라고 하는 대량의 데이터 집합 포함)를 관리하는 적절한 서비스를 선택하는 방법을 설명합니다.

관계형 기술은 오랫동안 데이터 작업을 위한 주요 방법이었습니다. 그러나 다양한 유형의 대량 데이터에 대한 작업을 수행할 경우에는 많은 서버에 걸쳐 크기를 조정하는 것이 어렵습니다. 또한 관계형 기술은 JSON 문서 또는 그래프와 같은 일부 데이터를 관리하는 데에는 적당하지 않습니다. 이 가이드에서는 옵션이 제공하는 기능과 사용 이유를 설명하면서 옵션을 차례로 소개합니다.

**내용:**

-   Azure의 데이터: 큰 그림
-   Azure 관계형 기술: 간략 보기
-   Azure NoSQL 기술: 작동 데이터

    -   키/값 저장소
    -   열 패밀리 저장소
    -   문서 저장소
    -   그래프 데이터베이스
-   Azure NoSQL 기술: 분석 데이터

    -   Hadoop MapReduce
    -   HDInsight

**작성자:** David Chappell, Chappell & Associates의 교장

**게시:** 2013년 11월

**수정 버전:** 1.0

**다운로드:** [Azure의 NoSQL 기술 이해 PDF 파일(영문)][]

  [Azure의 NoSQL 기술 이해 PDF 파일(영문)]: http://go.microsoft.com/fwlink/p/?LinkId=330292
