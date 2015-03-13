<properties 
    pageTitle="DocumentDB를 사용하여 Python 및 Flask로 웹 앱 작성 | Azure" 
    description="DocumentDB를 사용하여 Azure에 호스팅되는 Python 및 Flask(MVC) 웹 응용 프로그램의 데이터를 저장하고 액세스하는 방법에 대해 알아봅니다." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

<a name="_Toc395888515"></a><a name="_Toc395809324">DocumentDB를 사용하여 Python 및 Flask(MVC)로 웹 응용 프로그램 작성</a>
===========================================================================================================================================

<a name="_Toc395809324">

<a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">개요</a>
========================================================================================================================

<a name="_Toc395888517"></a><a name="_Toc395809326">시나리오</a>
----------------------------------------------------------------

고객이 Azure DocumentDB를 효율적으로 활용하여
JSON 문서를 저장 및 쿼리하는 방법을 강조하기 위해 이 문서에서는
Azure 문서 DB를 사용하여 투표 웹 응용 프로그램을 작성하는 완전한 연습을
제공합니다.

이 연습에서는 Azure에서 제공하는 DocumentDB 서비스를 사용하여
Azure에서 호스트되는 Python 웹 응용 프로그램의 데이터를 저장하고
액세스하는 방법을 보여 주며, 사용자가 이전에 Python 및 Azure 웹 사이트를
사용한 경험이 있다고 가정합니다.

다음 내용을 배웁니다.

1\. DocumentDB 계정 만들기 및 프로비전

2\. Python MVC 응용 프로그램 만들기

3\. 웹 응용 프로그램에서 Azure DocumentDB에 연결 및 사용

4\. Azure 웹 사이트에 웹 응용 프로그램 배포

이 연습을 수행하면 설문 조사를 위해 투표할 수 있는
간단한 투표 응용 프로그램이 작성됩니다.

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">필수 조건</a>


이 문서의 지침을 수행하기 전에 다음이 설치되어 있는지
확인해야 합니다.

Visual Studio 2013(또는 [Visual Studio Express][](무료
버전))

Python Tools for Visual Studio([여기][]서 사용 가능)

Azure SDK for Visual Studio 2013 버전 2.4 이상(
[여기][1]서 사용 가능)

Azure 플랫폼 간 명령줄 도구([Microsoft
웹 플랫폼 설치 관리자를 통해 사용 가능)][]

<a name="_Toc395888519"></a><a name="_Toc395809328">DocumentDB 데이터베이스 계정 만들기</a>
============================================================================================

Azure에서 DocumentDB 데이터베이스 계정을 프로비전하려면 Azure
관리 포털을 열고 홈 페이지에서 Azure 갤러리 타일을
클릭하거나 화면 왼쪽 하단에 있는 "+"를 클릭합니다.

![Alt text](./media/documentdb-python-application/image2.png)


그러면 많은 사용 가능한 Azure 서비스에서 선택할 수 있는
Azure 갤러리가 열립니다. 갤러리의 범주 목록에서 "데이터, 저장소 및
백업"을 선택합니다.

![Alt text](./media/documentdb-python-application/image3.png)

여기서 Azure DocumentDB에 대한 옵션을 선택합니다.

![Alt text](./media/documentdb-python-application/image4.png)

그런 다음 화면 맨 아래에서 "만들기"를 선택합니다.

![Alt text](./media/documentdb-python-application/image5.png)

그러면 새 계정의 이름, 지역, 크기 조정, 리소스 그룹 및 기타 설정을
지정할 수 있는 "새 DocumentDB" 블레이드가
열립니다.

![Alt text](./media/documentdb-python-application/image6.png)

계정의 값 입력을 완료한 후 "만들기"를 클릭하면
프로비전 프로세스에서 데이터베이스 계정 만들기를 시작합니다.
프로비전 프로세스가 완료되면 포털의 알림 영역에 알림이 표시되고
시작 화면의 타일(타일을 만들도록 선택한 경우)이
변경되어 완료된 작업을
표시합니다.

![Alt text](./media/documentdb-python-application/image7.png)

프로비전이 완료된 후 시작 화면에서 DocumentDB 타일을 클릭하면
이 새로 만든 DocumentDB 계정의 기본 블레이드가
표시됩니다.

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)



"키" 단추를 사용하여 끝점 URL 및 기본 키에 액세스하고
이를 클립보드에 복사하여 다음에 만들 웹 응용 프로그램에
이러한 값을 사용할 때 편리하도록 가까이 두세요.

</a>

<a name="_Toc395888520"></a><a name="_Toc395809329">새 Python Flask 웹 응용 프로그램 만들기</a>
=================================================================================================

Visual Studio, 파일 -\> 새 프로젝트 -\> Python -\>, 
이름이 **tutorial**인 Flask 웹 프로젝트를 엽니다. 그러면 외부 패키지를 설치할지
여부를 묻습니다. 가상 환경에 설치를 클릭한 후
만들기를 클릭합니다. 그러면 프로젝트에 필요한 Python 가상
환경이 설정됩니다.

Flask를 처음 사용하는 사용자를 위한 도움말입니다. Flask는
Python에서 웹 응용 프로그램을 더 빨리 작성하도록 도와주는 웹 프레임워크입니다. [Flask 자습서에 액세스하려면 여기를 클릭하세요][].

![Alt text](./media/documentdb-python-application/image10.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">프로젝트에 Flask 패키지 추가</a>
==================================

프로젝트가 설정되고 나면 프로젝트에 필요한
특정 Flask 패키지(예: 폼)를 추가해야 합니다. **env**
를 마우스 오른쪽 단추로 클릭하고 **다음 Python 패키지를 설치합니다(이 순서를 따르는 것이
중요함)**.

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**참고:** 출력 창에 실패가 표시되는 경우가 드물게 발생합니다. 그런
경우 오류가 정리와 관련이 있는지 확인하세요. 때때로 
정리는 실패하지만 설치가 성공적으로 수행되는 경우가 있습니다(출력 창에서
위로 스크롤하여 이를 확인).
<a name="verify-the-virtual-environment"></a> 이 경우 계속해도 됩니다.

</h1>
<a name="_Toc395888522"></a><a name="_Toc395809331">가상 환경 확인</a>
======================================================================================


모두 올바르게 설치되었는지 확인해 봅시다. 웹 사이트를
**F5**키를 눌러 시작합니다. 그러면 Flask 개발 서버와
웹 브라우저가 시작됩니다. 다음 페이지가 표시되어야 합니다.

![Alt text](./media/documentdb-python-application/image12.png)

<a name="_Toc395888523"></a><a name="_Toc395809332">프로젝트에 DocumentDB 추가</a>
=========================================================================================

DocumentDB Python SDK는 PyPi에 호스트되며 pip로 설치할 수
있습니다.

솔루션 탐색기에서 Python Environments 노드를 확장하고
사용자 환경을 마우스 오른쪽 단추로 클릭한 후 "Python 패키지 설치..."를 선택합니다.

![Alt text](./media/documentdb-python-application/image13.png)

PyPi 패키지의 이름인 "--pre pydocumentdb"를 입력합니다. 필요에 따라,
설치하려는 버전을 제어하려는 경우 알려진 버전을
제공할 수 있습니다. 버전을 제외하면 안정적인
최신 버전이 설치됩니다. 전체 이름 "--pre pydocumentdb"를 입력해야
한다는 사실을 명심하세요.

![Alt text](./media/documentdb-python-application/image14.png)

이제 pydocumentdb 패키지가 사용자 환경에 다운로드 및
설치됩니다. 완료되면 사용자 환경 아래에 pydocumentdb가 모듈로
나열되어야 합니다.

</h1>
<a name="_Toc395888524"></a><a name="_Toc395809333">데이터베이스, 컬렉션 및 문서 정의 만들기</a>
============================================================================================================

다음을 python 파일 **forms.py**에 추가하고 솔루션 탐색기에서
이 파일을 tutorial이라는 폴더에 추가합니다. 다음 코드를
forms.py에 추가합니다. 현재 폴링 응용 프로그램을 만들고 있습니다. 이렇게 하려면
사용자 입력을 기반으로 전환되는 세 개의 부울 필드를 만들면 됩니다.

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">데이터베이스, 컬렉션 및 문서 만들기</a>

\#참고: 인증 자격 증명은 앱이 아니라 구성 파일에 저장하는 것이
더 안전한 것으로 간주됩니다. 간단히 하기 위해 여기서는
인증 자격 증명을 소스 코드에 배치합니다. views.py에 새 함수를 만들고 이름을
create로 지정합니다. 다음을 **views.py**에 추가합니다. 기존 코드의 어떠한 부분도
삭제하지 마세요.

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">사용자 투표 수락 및 문서 업데이트</a>
=================================================================================================

### <a name="_Toc395809338">필수 가져오기 추가

<a name="_Toc395888529"></a>
============================

**views.py**의 맨 위에 다음 import 문을 추가합니다. 그러면
DocumentDB의 PythonSDK 및 Flask 패키지를 가져옵니다.


    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">데이터베이스, 컬렉션 및 문서 읽기</a>

**views.py**에 다음 코드를 추가합니다. 이 코드는 폼 설정 및
데이터베이스, 컬렉션 및 문서 읽기를 처리합니다. views.py 기존
코드의 어떠한 부분도 삭제하지 마세요. 이 코드를 끝에 추가하기만 하면 됩니다.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">결과 표시</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">html 파일 만들기</a>

템플릿 폴더 아래에 다음 html 파일을 추가합니다.
create.html, results.html, vote.html

**create.html**에 다음 코드를 추가합니다. 이 코드는 새 데이터베이스,
컬렉션 및 문서를 만들었다는 메시지 표시를 처리합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

**results.html**에 다음 코드를 추가합니다. 이 코드는 설문 조사 결과
표시를 처리합니다.
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

**vote.html**에 다음 코드를 추가합니다. 이 코드는 설문 조사 표시
및 투표 수락을 처리합니다. 투표 등록 시 컨트롤이
views.py에 전달됩니다. 여기서 투표가 진행되었고 적절하게
문서가 추가되었음을 알 수 있습니다.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

**layout.html**의 코드를 삭제하고 다음 코드로 바꿉니다. 이 코드는
탐색 모음 및 라우팅을 위한 해당 URL 설정을 처리합니다.

index.html의 코드를 삭제하고 다음 코드로 바꿉니다. 이 코드는
응용 프로그램의 방문 페이지 역할을 합니다.

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">구성 파일 추가 및 \_\_init\_\_.py 변경</a>
----------------------------------------------------------------------------------------------------------------

프로젝트 이름 tutorial을 마우스 오른쪽 단추로 클릭하고 **config.py** 파일을 추가합니다.
이 구성은 Flask의 폼에 필요합니다. 이를 사용하여
비밀 키를 제공할 수도 있습니다. config.py에 다음 코드를 추가합니다.
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

마찬가지로 **\_\_init\_\_.py 파일**을 추가합니다. tutorial이라는
폴더 아래에서 이 파일을 찾습니다. 원래 코드를 다음 코드로 바꿉니다. 이 코드는
뷰와 config.py 파일 간의 연결을 처리합니다.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

위에서 언급한 단계를 수행하고 나면 솔루션 탐색기는
다음과 같습니다.

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">로컬에서 응용 프로그램 실행</a>
============================

Visual Studio에서 F5 키나 실행 단추를 누르면 화면에
다음이 표시됩니다.

![Alt text](./media/documentdb-python-application/image16.png)

투표를 클릭하고 옵션을 선택합니다.

![Alt text](./media/documentdb-python-application/image17.png)

투표할 때마다 해당 카운터가 증가합니다. 다음에
투표할 때 결과를 확인할 수 있습니다.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Azure 웹 사이트에 응용 프로그램 배포</a>
========================================================================================================================

이제 DocumentDB에 대해 올바르게 작동하는 전체 응용 프로그램이
있으므로 이 응용 프로그램을 Azure 웹 사이트에 배포하겠습니다. 솔루션 탐색기에서
프로젝트를 마우스 오른쪽 단추로 클릭하고(로컬에서 실행하고
있지 않은지 확인) 게시를 선택합니다.

![Alt text](./media/documentdb-python-application/image19.png)


자격 증명을 제공하고 새 웹 사이트를 만들거나 이전 웹 사이트를
다시 사용하여 Azure 웹 사이트를 구성합니다.

![Alt text](./media/documentdb-python-application/image20.png)


몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를
완료하고 브라우저를 시작하며, Azure에서 실행되는 작업 내용을
확인할 수 있습니다.

</h1>
<a name="_Toc395809338">결론</a>
==========

축하합니다. 방금 Azure DocumentDB를 사용하여 첫 번째
Python 응용 프로그램을 작성하고 Azure 웹 사이트에 게시했습니다.

전체 솔루션을 보려면 [여기를 클릭][]하세요. 참고: 여전히
앞에서 언급한 대로 가상 환경을 추가하고 python 도구 및 패키지를
설치해야 합니다.)

</h1>

  [여기를 클릭]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Flask 자습서에 액세스하려면 여기를 클릭하세요.]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/ko-kr/products/visual-studio-express-vs.aspx
  [시작]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft 웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx

<!--HONumber=46--> 
