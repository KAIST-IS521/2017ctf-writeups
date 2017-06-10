 - Snort
 
    수업시간에 설명한 web application관 관련된 취약점  snort을 적용하였음    
    그리고 대회를 진행하면서 팀에서 찾은 취약점과 관련된 취약점에 해당하는 룰을 추가하였음
    
    reject tcp any any -> any any (msg:"SQL Injection - Paranoid"; flow:to_server,established;uricontent:".pl";pcre:"/(\%27)|(\')|(\-\-)|(%23)|(#)/i"; sid:9099; rev:5;)  
    reject tcp any any -> any any (msg:"NII Cross-site scripting attempt"; flow:to_server,established; pcre:"/((\%3C)|<)((\%2F)|\/)*[a-z0-9\%]+((\%3E)|>)/i"; sid:9000; rev:5;)  
    reject tcp any any -> any any (msg:"read file"; pcre:"/(\.\.\/)/i";sid:10000;rev:5)  
