데이터는 XML 데이터로 저장되는 각 S&P 500 회사의 자료에 따라 Wikipedia(<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>)에서 파생됩니다.<p> </p>Azure ML Studio로 업로드하기 전에 데이터 집합이 다음과 같이 처리됩니다.<ul><li>각 특정 회사에 대한 텍스트 콘텐츠 추출</li><li>위치 형식 지정 제거</li><li>영숫자가 아닌 문자 제거</li><li>모든 텍스트를 소문자로 변환</li><li>알려진 회사 범주가 추가됨</li></ul><p> </p>일부 회사에 대한 자료는 찾을 수 없으므로 레코드 수는 500개 미만입니다.


<!--HONumber=42-->
