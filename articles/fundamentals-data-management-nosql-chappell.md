<properties urlDisplayName="Understand NoSQL technologies" pageTitle="Azure의 NoSQL 기술 이해 | Azure" metaKeywords="" description="HDInsight의 NoSQL 기술을 통해 빅 데이터 집합 및 JSON 문서나 그래프와 같이 관계형 데이터베이스에 적합하지 않은 데이터를 관리하는 방법에 대해 알아봅니다." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight,documentdb" documentationCenter="" title="Data management: Understanding NoSQL technologies on Azure" authors="cgronlun" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/01/2014" ms.author="cgronlun" />

# 데이터 관리: Microsoft Azure의 NoSQL 이해

**요약:** Azure는 NoSQL 작업에 대한 다양한 옵션을 제공하지만, 사용 가능한 모든 옵션 간의 차이점을 구분하기는 어려울 수 있습니다. 이 문서에서는 Azure의 NoSQL 기술에 대해 설명하고 관계형 모델에서 데이터를 관리할 때와 어떻게 다른지 설명합니다.

관계형 기술은 오랫동안 데이터 작업을 위한 주요 방법이었습니다. 그러나 오늘날 그 사례가 점점 더 늘고 있는 대량의 여러 데이터 유형을 작업할 때는 이 데이터를 관리하는 데 따른 새로운 과제에 직면하게 됩니다. 관계형 기술은 이 데이터를 관리하는 데 가장 적합한 경우도 있고 그렇지 않은 경우도 있습니다. 이 가이드에서는 옵션이 제공하는 기능과 사용 이유를 설명하면서 옵션을 차례로 소개합니다.


**내용:**

* Azure의 데이터: 큰 그림	
* Azure 관계형 기술: 빠른 검토	
* Azure NoSQL 기술
	* 문서 저장소: DocumentDB
	* 키/값 저장소: 테이블
	* 열 패밀리 저장소: HBase
	* 빅데이터 분석: Oozie 코디네이터 사용 


**작성자:** David Chappell, Chappell & Associates의 대표자

**게시 날짜:** 2014 년 11 월

**수정 버전:** 2.0

**다운로드:** <a href="http://go.microsoft.com/fwlink/p/?LinkId=330292" target="_blank">"Microsoft Azure의 NoSQL 이해" PDF 파일</a>

<!--HONumber=35.2-->