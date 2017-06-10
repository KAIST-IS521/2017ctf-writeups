# CTF Write Up
=====

- Bank Service


  diff command를 이용하여서 bank 서비스의 정규식 관련부분을 확인하였음

  > REGEX_USERNAME = '^[A-Za-z0-9_]{,20}$'  
  > #REGEX_PASSWORD = '' # TODO: should be changed based on MySQL  
  > REGEX_EMAIL = '(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)'  
  > REGEX_PHONE = '^\d{11}$'  
  > REGEX_MSG = '^[A-za-z0-9\.\,_\(\)\' ]{,100}$'  
  > REGEX_AMOUNT = '^[0-9]*$'  
  
  
  여기서 REGEX_MSG 부분을 확인하면 sql injection에 사용될수 '(싱글쿼터)가 허용되어 있음  
  
  bank에 해당하는 flag 값은 admin계정의 email에 존재한다고 하였음  
  
  msg관련 쿼리가 실행되는 부분에 msg = ', (select email from user_table where user_id like 'admin'), ' 와같이    
  user_table  테이블에서 user_id가 admin인 이메일을 출력하게 하는 쿼리를 flag 값을 획득할 수 있음
  
 - Visual web

   web python 코드를 확인해보면 file_read 관련 함수가 존재함  
   
   >@app.route("/get",methods=['GET'])  
   >def static_page():  
   >if 'page' in flask.request.args:  
   >	    page = flask.request.args.get('page')  
   >	    path = 'static/' + page  
   >	    if os.path.exists(path) and not os.path.isdir(path):  
   >		return file_read(path)  
     
   
   확인 get 방식으로 들어오는 파일의  "/get" 규칙으로 들어오는 page의 경로를 읽어오는 함수임  
   그렇기에 ../ 와같이 이전 디렉토리를 검색하면서 디렉토리의 위치를 파악함  
   /var/ctf/flag 에 flag가 있음. /etc/passwd나 시스템에 존재해야하는 파일들을 읽으으로서 루트 디렉토리 위치 파악
   /get?page=../../../../../../../../../../var/ctf/flag 파악후 위와같이 페이지에 인자값을 넘겨주면 키값 획득  
   
  
 - Snort
 
    수업시간에 설명한 web application관 관련된 취약점  snort을 적용하였음    
    그리고 대회를 진행하면서 팀에서 찾은 취약점과 관련된 취약점에 해당하는 룰을 추가하였음
    
    reject tcp any any -> any any (msg:"SQL Injection - Paranoid"; flow:to_server,established;uricontent:".pl";pcre:"/(\%27)|(\')|(\-\-)|(%23)|(#)/i"; sid:9099; rev:5;)  
    reject tcp any any -> any any (msg:"NII Cross-site scripting attempt"; flow:to_server,established; pcre:"/((\%3C)|<)((\%2F)|\/)*[a-z0-9\%]+((\%3E)|>)/i"; sid:9000; rev:5;)  
    reject tcp any any -> any any (msg:"read file"; pcre:"/(\.\.\/)/i";sid:10000;rev:5)  
