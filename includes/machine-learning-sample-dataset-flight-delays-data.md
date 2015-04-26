미국 운수부 TranStats 데이터 컬렉션에서 가져온 여객기 운항정시성 데이터(<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">정시</a>(영문)).<p> </p>데이터 집합은 2013년 4월-10월 기간에 해당합니다. Azure ML Studio로 업로드하기 전에 데이터 집합이 다음과 같이 처리됩니다.<ul><li>데이터 집합은 미국 본토에서 비행이 가장 많은 공항 70곳만을 포함하도록 필터링되었습니다.</li><li>취소된 비행은 15분 초과 지연으로 레이블이 지정되었습니다.</li><li>우회 비행은 필터링되었습니다.</li><li>다음 열이 선택되었습니다. Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelled</li></ul>


<!--HONumber=42-->
