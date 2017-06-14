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
