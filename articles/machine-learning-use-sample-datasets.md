<properties title="Use the sample datasets in Azure Machine Learning Studio" pageTitle="Use the sample datasets in Machine Learning Studio | Azure" description="Use the sample datasets in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure 기계 학습 스튜디오에서 샘플 데이터 집합 사용

기계 학습 스튜디오에 들어 있는 몇 가지 샘플 데이터 집합을 사용할 수 있습니다. 이러한 데이터 집합은 공용 도메인의 표준 기계 학습 데이터 집합입니다.

기계 학습 스튜디오에서 다양한 유형의 분석 기계 학습 모델을 살펴보는 것처럼 실험에서 이러한 데이터 집합을 사용할 수 있습니다.

-   기계 학습 스튜디오에서 이러한 표준 데이터 집합 목록을 **데이터 집합** 탭을 클릭합니다. 각 데이터 집합에 대한 데이터 집합 이름, 제출한 사람, 간략한 설명 같은 정보를 볼 수 있습니다.

    데이터 집합을 정렬하려면 열 머리글을 클릭합니다. 예를 들어 **제출자**를 클릭하여 Microsoft Corporation에서 제공하는 모든 샘플 데이터 집합을 그룹화합니다. 이렇게 하면 작업 영역에서 자신과 다른 사용자가 가져온 데이터 집합을 간편하게 볼 수 있습니다.

-   실험에서 데이터 집합을 사용하려면 모듈 팔레트의 **저장된 데이터 집합** 섹션을 실험 캔버스 왼쪽으로 확장하거나, 팔레트 위의 검색 상자에 이름을 입력하여 특정 데이터 집합을 검색합니다. 데이터 집합을 캔버스로 끌어서 실험에 추가합니다.

## 샘플 데이터 집합

다음 표에서는 기계 학습 스튜디오의 기계 학습 기능을 살펴보는 동안 유용할 수 있는 데이터 집합에 대한 추가 정보를 제공합니다. 표에는 각 데이터 집합에 대한 다음 정보가 나와 있습니다.

-   각 데이터 집합의 이름 및 원본.
-   데이터 집합의 가능한 사용에 대한 설명 및 데이터 집합을 사용한 기계 학습 조사에 대한 인용.
-   데이터 집합에 포함된 중요한 열의 요약 및 기타 유용한 특성.

몇몇 설명은 원본(일반적으로 [UC Irvine Machine Learning Repository][UC Irvine Machine Learning Repository])에서 제공된 설명에서 조정되었으나 기타 설명은 추가 조사를 기반으로 하거나 이러한 샘플에서 변경된 내용을 반영합니다.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">데이터 집합 이름</th>
<th align="left">데이터 집합 설명</th>
<th align="left">관련 조사</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">성인 인구 조사 소득 이진 분류 데이터 집합</td>
<td align="left"><p>조정 소득 지수가 100보다 큰 16세 이상 취업한 성인을 대상으로 한 1994 인구 조사 데이터베이스의 하위 집합입니다.</p>
<p><strong>Usage:</strong> 인구 통계를 기반으로 사람을 분류하여 개인의 소득이 연간 50,000을 초과할지 예측합니다.</p></td>
<td align="left"><p>Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="even">
<td align="left"><p>자동차 가격 데이터(원시)</p></td>
<td align="left"><p>가격, 실린더 및 MPG 수와 같은 기능, 보험 위험 점수를 포함하여 상표 및 모델별 자동차에 대한 정보입니다.</p>
<p>위험 점수는 처음에 자동차 가격과 관련되고 보험 회계사에게 <legacyitalic>기호화</legacyitalic>로 알려진 프로세스에서 실제 위험에 맞게 조정됩니다. +3 값은 자동차가 위험함을 나타내고 -3 값은 매우 안전함을 나타냅니다.</p>
<p><strong>Usage:</strong> 회귀 및 다변수 분류를 사용하여 기능별 위험 점수를 예측합니다.</p></td>
<td align="left"><p>Schlimmer, J.C. (1987). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>헌혈 데이터</p></td>
<td align="left"><p>대만, Hsin-Chu City, Blood Transfusion Service Center 헌혈자 데이터베이스의 데이터 하위 집합입니다.</p>
<p>현혈자 데이터에는 마지막 헌혈 이후 지난 개월 수, 빈도 또는 총 헌혈 횟수, 마지막 헌혈 이후 시간, 헌혈량이 포함됩니다.</p>
<p><strong>Usage:</strong> 목표는 헌혈자가 2007년 3월 헌혈을 했는지를 분류를 통해 예측하는 것입니다. 여기서 1은 목표 기간 중 헌혈자를 나타내고 0은 비헌혈자를 나타냅니다.</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Yeh, I.C., (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
</listitem> <listitem>
<p>Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, &quot;Knowledge discovery on RFM model using Bernoulli sequence, &quot;Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018" class="uri">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>유방암 데이터</p></td>
<td align="left"><p>기계 학습 문헌에서 자주 나타나는 Oncology Institute에서 제공하는 세 가지 암 관련 데이터 집합의 하나입니다. 진단 정보를 300여 개 조직 샘플에 대한 실험실 분석의 기능과 결합합니다.</p>
<p><strong>Usage:</strong> 특성 9개에 따라 암 유형을 분류합니다. 이들 중 몇몇은 선형이고 몇몇은 범주입니다.</p></td>
<td align="left"><p>Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>에너지 효율 회귀 데이터</p></td>
<td align="left"><p>12가지 건물 형태에 따라 시뮬레이트된 에너지 프로필의 컬렉션입니다. 건물은 창호 면적, 창호 면적 분포, 방향 같은 8개의 특징으로 구분된다는 점에서 다릅니다.</p>
<p><strong>Usage:</strong> 회귀 또는 분류를 사용하여 두 가지 실제 값 응답의 하나를 기반으로 에너지 효율 등급을 예측합니다. 다중 클래스 분류에 대해서는 가장 가까운 정수로 응답 변수를 반올림합니다.</p></td>
<td align="left"><p>Xifara, A. &amp; Tsanas, A. (2012). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="even">
<td align="left"><p>비행 운항정시성(원시)</p></td>
<td align="left"><p>2011년 10월부터 미국 내의 비행 도착 및 출발 레코드입니다.</p>
<p><strong>Usage:</strong> 비행 지연을 예측합니다.</p></td>
<td align="left"><p>미국 운수부 [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time" class="uri">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>산불 데이터</p></td>
<td align="left"><p>산불 레코드와 결합된 포르투갈 북동부 지역의 기온 및 습도 지수, 풍속 같은 날씨 데이터를 포함합니다.</p>
<p><strong>Usage:</strong> 이 작업은 산불 피해지를 예측하기 위한 어려운 회귀 작업입니다.</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Cortez, P., &amp; Morais, A. (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
</listitem> <listitem>
<p>[Cortez and Morais, 2007] P. Cortez and A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. In J. Neves, M. F. Santos and J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Available at: [<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf" class="uri">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>붓꽃 2종 데이터</p></td>
<td align="left"><p>이 데이터베이스는 패턴 인식 문헌에서 찾을 수 있는 가장 잘 알려진 데이터베이스일 것입니다. 데이터 집합은 비교적 작으며, 세 가지 붓꽃 품종의 꽃잎 측정값별로 예제 50개를 포함합니다.</p>
<p><strong>Usage:</strong> 측정값에서 붓꽃 종류를 예측합니다. <!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now. One class is linearly separable from the other two; but the latter are not linearly separable from each other. --></p></td>
<td align="left"><p>Fisher, R.A. (1988). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>다양한 자동차에 대한 MPG 데이터</p></td>
<td align="left"><p>이 데이터 집합은 Carnegie Mellon University의 StatLib 도서관에서 제공한 데이터 집합의 약간 수정된 버전입니다. 이 데이터 집합은 1983 American Statistical Association Exposition에서 사용되었습니다.</p>
<p>데이터에는 다양한 자동차에 대한 연료 소비량(m.p.g), 실린더 수, 엔진 배기량, 마력, 총 중량, 가속도가 함께 나열됩니다.</p>
<p><strong>Usage:</strong> 다중 값 개별 특성 3개 및 연속 특성 5개를 기반으로 연비를 예측합니다.</p></td>
<td align="left"><p>StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="even">
<td align="left"><p>피마 인디언 당뇨병 이진 분류 데이터 집합</p></td>
<td align="left"><p>NIDDK(National Institute of Diabetes and Digestive and Kidney Diseases) 데이터베이스의 데이터 하위 집합입니다. 데이터 집합은 피마 인디언 혈통의 여성 환자에게 초점을 맞추도록 필터링되었습니다. 데이터에는 포도당 및 인슐린 수준 같은 의료 데이터와 생활양식 요인이 포함됩니다.</p>
<p><strong>Usage:</strong> 대상에게 당뇨병이 있는지를 예측합니다(이진 분류).</p></td>
<td align="left"><p>Sigillito, V. (1990). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>음식점 고객 데이터</p></td>
<td align="left"><p>인구 통계 및 선호도를 비롯한 고객에 대한 메타데이터 집합입니다.</p>
<p><strong>Usage:</strong> 이 데이터 집합을 다른 두 가지 음식점 데이터 집합과 함께 사용하여 추천 시스템을 학습 및 테스트합니다.</p></td>
<td align="left"><p>Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science.</p></td>
</tr>
<tr class="even">
<td align="left"><p>음식점 기능 데이터</p></td>
<td align="left"><p>음식점 및 음식 종료, 식사 스타일, 위치 같은 기능에 대한 메타데이터 집합입니다.</p>
<p><strong>Usage:</strong> 이 데이터 집합을 다른 두 가지 음식점 데이터 집합과 함께 사용하여 추천 시스템을 학습 및 테스트합니다.</p></td>
<td align="left"><p>Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>음식점 등급</p></td>
<td align="left"><p>0-2점 사이에 사용자가 제공한 음식점 등급을 포함합니다.</p>
<p><strong>Usage:</strong> 이 데이터 집합을 다른 두 가지 음식점 데이터 집합과 함께 사용하여 추천 시스템을 학습 및 테스트합니다.</p></td>
<td align="left"><p>Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science.</p></td>
</tr>
<tr class="even">
<td align="left"><p>강철 가열 냉각 다중 클래스 데이터 집합</p></td>
<td align="left"><p>이 데이터 집합은 강철 가열 냉각 시도에서 얻은 일련의 레코드를 결과 강철 유형의 물리적 특성(너비, 두께, 유형(코일, 시트 등))과 함께 포함합니다.</p>
<p><strong>Usage:</strong> 두 가지 숫자 클래스 특성인 경도 또는 강도의 하나를 예측합니다. 특성 간의 상관 관계를 분석할 수도 있습니다.</p>
<p>강철 등급은 SAE 및 기타 조직에서 정의된 집합 표준을 따릅니다. 특정 '등급'(클래스 변수)을 찾고 있고 필요한 값을 파악하려고 합니다.</p></td>
<td align="left"><p>Sterling, D. &amp; Buntine, W., (NA). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
<p>강철 등급에 대한 유용한 가이드:</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf&gt;]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>망원경 데이터</p></td>
<td align="left"><p>배경 소음과 함께 고에너지 감마 미립자 폭발에 대한 레코드로서, 두 항목 모두 Monte Carlo 프로세스를 사용하여 시뮬레이트됩니다.</p>
<p>시뮬레이션의 의도는 통계적 방법을 사용하여 원하는 신호(체렌코프 방사선 샤워)와 배경 소음(상층부 대기의 우주 방사선에서 시작된 하드론 샤워)을 구분하는 지상 대기 체렌코프 감마 망원경을 향상하는 것이었습니다.</p>
<p>데이터는 긴 축이 카메라 중심을 향하도록 하여 가늘고 긴 클러스터를 만들도록 전처리되었습니다. 이 타원의 특성(Hillas 매개 변수라고도 함)은 구분에 사용할 수 있는 이미지 매개 변수 중 하나입니다.</p>
<p><strong>Usage:</strong> 샤워 이미지가 신호 또는 배경 소음을 나타내는지를 예측합니다.</p>
<p><strong>참고:</strong> 배경 이벤트를 신호로 분류하는 것은 신호 이벤트를 배경으로 분류하는 것보다 비효율적이므로 단순 분류 정확도는 이 데이터에 대해 의미가 없습니다. 다양한 분류자를 비교하려면 ROC 그래프를 사용해야 합니다. 배경 이벤트를 신호로 수락할 가능성은 임계값 0.01 , 0.02 , 0.05 , 0.1 또는 0.2의 하나보다 낮아야 합니다.</p>
<p>또한 배경 이벤트 수(h, 하드론 샤워의 경우)는 과소 예측되지만 실제 측정값에서 h 또는 소음 클래스는 대부분 이벤트를 나타냅니다.</p></td>
<td align="left"><p>Bock, R.K. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
</tbody>
</table>

발견할 수 있는 데이터 집합 형식은 다음과 같습니다.

| 데이터 형식                              | 설명                                                                                                                                                                                             |
|------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CSV(쉼표로 구분된 값)                    | 모든 데이터 플랫폼에 대한 잘 알려진 교환 형식입니다. 기계 학습 스튜디오에서는 이 데이터를 로드하여 각 열에서 샘플링된 데이터를 기반으로 한 메타데이터를 적용하는 열 범위 유형 추측을 통합합니다. |
| ARFF(Attribute Relationship File Format) | WEKA에서 정의한 기계 학습 데이터 형식입니다. 기계 학습 스튜디오 데이터 테이블로 직접 변환되는 메타데이터(명목, 숫자 및 문자열 형식)를 포함합니다.                                                |
| 일반 텍스트                              | 일반 텍스트를 읽고 다운스트림 전처리 모듈을 통해 열로 분할할 수 있습니다. 지원되는 주요 일반 텍스트 형식은 JSON입니다.                                                                           |
| DotNetTable                              | 기계 학습 스튜디오에서 모듈 간에 전달된 기본 컨테이너의 직렬화된 버전입니다(*데이터 집합*).                                                                                                      |

  [UC Irvine Machine Learning Repository]: http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository"
