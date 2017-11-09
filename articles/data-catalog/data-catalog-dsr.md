---
title: "Azure Data Catalog에서 지원되는 데이터 원본 | Microsoft Docs"
description: "이 문서에서는 현재 지원되는 데이터 원본의 사양을 나열합니다."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: 1f637acffdbf31f6c69124282f50dc5830ad9f5b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Azure Data Catalog에서 지원되는 데이터 원본

사용자는 공용 API를 사용하거나 등록 도구 클릭 한 번으로 Azure Data Catalog 웹 포털에 정보를 직접 입력하여 메타데이터를 게시할 수 있습니다. 다음 테이블은 현재 카탈로그로 지원되는 모든 데이터 원본 및 각각에 대한 게시 기능을 요약합니다. 또한 포털의 "열기" 경험에서 시작할 수 있는 각 데이터 원본에 대한 외부 데이터 도구가 나열되어 있습니다. 두 번째 테이블에는 각 데이터 원본 연결 속성의 자세한 기술 사양이 포함되어 있습니다.


## <a name="list-of-supported-data-sources"></a>지원되는 데이터 원본 목록

<table>
    <tr>
       <td><b>데이터 원본 개체</b></td>
       <td><b>API</b></td>
       <td><b>수동 입력</b></td>
       <td><b>등록 도구</b></td>
       <td><b>도구에서 열기</b></td>
       <td><b>참고 사항</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store 파일</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Blob 저장소</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>HDFS 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HDFS 파일</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive 보기</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle 데이터베이스 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle 데이터베이스 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>기타(일반 자산)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure SQL Data Warehouse 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server 데이터 도구</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Data Warehouse 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server 데이터 도구</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 차원</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 측정값</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, 파워 BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services 보고서</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>브라우저</font></td>
      <td><font size=2>기본 모드 서버에만 해당. SharePoint 모드는 지원되지 않음.</font></td>
    </tr>
    <tr>
      <td>SQL Server 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server 데이터 도구</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server 데이터 도구</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>파일 시스템 파일</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP 파일</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 보고서</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 끝점</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 파일</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 엔터티 집합</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 함수</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA 뷰</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Salesforce 개체</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SharePoint 목록 </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Cosmos DB 컬렉션</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>일반 ODBC 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>일반 ODBC 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Cassandra 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>일반 ODBC 자산으로 게시</font></td>
    </tr>
    <tr>
      <td>Cassandra 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>일반 ODBC 자산으로 게시</font></td>
    </tr>
    <tr>
      <td>Sybase 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Sybase 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MongoDB 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>일반 ODBC 자산으로 게시</font></td>
    </tr>
    <tr>
      <td>MongoDB 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>일반 ODBC 자산으로 게시</font></td>
    </tr>
</table>

추가적인 원본에 대한 지원이 필요하면, [Azure Data Catalog 포럼](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)에 기능 요청을 제출합니다.


## <a name="data-source-reference-specification"></a>데이터 원본 참조 사양
> [!NOTE]
> 다음 테이블의 **DSL 구조** 열에서는 Azure Data Catalog에서 사용되는 "address" 속성 모음에 대한 연결 속성을 나열합니다. 즉, "address" 속성 모음에는 Azure Data Catalog에서 지속하지만 사용하지 않는 데이터 원본의 다른 연결 속성이 포함될 수 있습니다.

<table>
    <tr>
       <td><b>원본 유형</b></td>
       <td><b>자산 형식</b></td>
       <td><b>개체 유형</b></td>
       <td><b>DSL 구조<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>컨테이너</td>
      <td>데이터 레이크</td>
      <td>
        <font size=2> 프로토콜: webhdfs <br>인증: {기본, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>테이블</td>
      <td>디렉터리, 파일</td>
      <td>
        <font size=2> 프로토콜: webhdfs <br>인증: {기본, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>컨테이너</td>
      <td>컨테이너</td>
      <td>
        <font size=2> 프로토콜: azure-blobs <br>인증: {azure-access-key} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 도메인 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 계정 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 컨테이너 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>테이블</td>
      <td>Blob, 디렉터리</td>
      <td>
        <font size=2> 프로토콜: azure-blobs <br>인증: {azure-access-key} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 도메인 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 계정 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 컨테이너 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 이름 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>컨테이너</td>
      <td>컨테이너</td>
      <td>
        <font size=2> 프로토콜: azure-tables <br>인증: {azure-access-key} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 도메인 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 계정 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>테이블</td>
      <td>테이블</td>
      <td>
        <font size=2> 프로토콜: azure-tables <br>인증: {azure-access-key} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 도메인 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 계정 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 이름 </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>컨테이너</td>
      <td>가상 클러스터</td>
      <td>
        <font size=2> 프로토콜: cosmos <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>테이블</td>
      <td>스트림, 스트림 집합, 뷰</td>
      <td>
        <font size=2> 프로토콜: cosmos <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>컨테이너</td>
      <td>사이트</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>보고서</td>
      <td>보고서, 대시보드</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: db2 <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: db2 <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 </font>
      </td>
    </tr>
    <tr>
      <td>파일 시스템</td>
      <td>테이블</td>
      <td>파일</td>
      <td>
        <font size=2> 프로토콜: 파일 <br>인증: {없음, 기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 경로 </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>테이블</td>
      <td>디렉터리, 파일</td>
      <td>
        <font size=2> 프로토콜: ftp <br>인증: {없음, 기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>컨테이너</td>
      <td>프로비전</td>
      <td>
        <font size=2> 프로토콜: webhdfs <br>인증: {기본, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>테이블</td>
      <td>디렉터리, 파일</td>
      <td>
        <font size=2> 프로토콜: webhdfs <br>인증: {기본, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: hive <br>인증: {hdinsight, 기본, 사용자 이름, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: hive <br>인증: {hdinsight, 기본, 사용자 이름, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>http</td>
      <td>컨테이너</td>
      <td>사이트</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>http</td>
      <td>보고서</td>
      <td>보고서, 대시보드</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>http</td>
      <td>테이블</td>
      <td>끝점, 파일</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: mysql <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: mysql <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>컨테이너</td>
      <td>엔터티 컨테이너</td>
      <td>
        <font size=2> 프로토콜: odata <br>인증: {없음, 기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>테이블</td>
      <td>엔터티 집합, 함수</td>
      <td>
        <font size=2> 프로토콜: odata <br>인증: {없음, 기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 리소스 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle 데이터베이스</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: oracle <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle 데이터베이스</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: oracle <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: postgresql <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: postgresql <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>컨테이너</td>
      <td>사이트</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>보고서</td>
      <td>보고서, 대시보드</td>
      <td>
        <font size=2> 프로토콜: http <br>인증: {없음, 기본, windows, oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>파워 쿼리</td>
      <td>테이블</td>
      <td>데이터 매시업</td>
      <td>
        <font size=2> 프로토콜: power-query <br>인증: {oauth} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>테이블</td>
      <td>Object</td>
      <td>
        <font size=2> 프로토콜: salesforce-com <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>컨테이너</td>
      <td>서버</td>
      <td>
        <font size=2> 프로토콜: sap-hana-sql <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>테이블</td>
      <td>보기</td>
      <td>
        <font size=2> 프로토콜: sap-hana-sql <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>테이블</td>
      <td>나열</td>
      <td>
        <font size=2> 프로토콜: sharepoint-list <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>명령</td>
      <td>저장 프로시저</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>테이블 반환 함수</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>명령</td>
      <td>저장 프로시저</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>테이블 반환 함수</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: tds <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>컨테이너</td>
      <td>모델</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>측정값</td>
      <td>측정값</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {측정값} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>테이블</td>
      <td>차원</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {차원} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>컨테이너</td>
      <td>모델</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>측정값</td>
      <td>측정값</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {측정값} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>테이블</td>
      <td>테이블</td>
      <td>
        <font size=2> 프로토콜: analysis-services <br>인증: {windows, 기본, 익명, 없음} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {테이블} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>컨테이너</td>
      <td>서버</td>
      <td>
        <font size=2> 프로토콜: reporting-services <br>인증: {windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 버전: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>보고서</td>
      <td>보고서</td>
      <td>
        <font size=2> 프로토콜: reporting-services <br>인증: {windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 경로 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 버전: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: teradata <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: teradata <br>인증: {프로토콜, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>컨테이너</td>
      <td>모델</td>
      <td>
        <font size="2"> 프로토콜: mssql-mds <br>인증: {windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 버전 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>테이블</td>
      <td>엔터티</td>
      <td>
        <font size="2"> 프로토콜: mssql-mds <br>인증: {windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 모델 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 버전 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 엔터티 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: document-db <br>인증: {azure-access-key} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>컬렉션</td>
      <td>컬렉션</td>
      <td>
        <font size=2> 프로토콜: document-db <br>인증: {azure-access-key} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 컬렉션 </font>
      </td>
    </tr>
    <tr>
      <td>일반 ODBC</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: odbc <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 옵션 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>일반 ODBC</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: odbc <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 옵션 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: sybase <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: sybase <br>인증: {기본, windows} <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 서버 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 데이터베이스 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 스키마 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 개체 </font>
      </td>
    </tr>
    <tr>
      <td>기타(위에 해당 없음)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> 프로토콜: generic-asset <br>주소: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>
