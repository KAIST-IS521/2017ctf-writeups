# Main Role
- Team-4에서는 notary program과 launcher program으로 구성되어 있는데 그 중 launcher program 부분을 담당했습니다.

## launcher program 
- 요청이 들어오는 IP 주소의 범위가 실행시 설정한 범위에 포함되는지 처리하고 포함되지 않으면 오류를 처리하는 부분
- 8001번 포트를 열어 JSON-based request를 받아 처리한 뒤 성공 여부를 reply하는 부분
- 받은 JSON-based data의 name과 body 부분을 parsion하는 부분
- parsing한 base64 encoded data를 복호화 하는 부분
- 다른 학생이 해당 data가 notary public key로 복호화하는 부분을 처리한 뒤에 그 파일을 binary file로 저장하는 부분
- 다른 학생이 만든 launcher의 나머지 부분과 통합하는 부분
- 가상으로 Client를 만들어 launcher program의 작동 여부를 확인하는 부분

## CTF
- CTF 준비기간과 도중에 수술을 마치고 몸 상태가 많이 좋지 않아 기여한 바가 거의 없습니다.
- 그래서 Team1부터 Team5까지의 프로그램들을 실행해보고 Team1과 Team2의 소스를 중심으로 공격 취약점을 분석했습니다.
- Team1의 소스를 보고 Database를 사용하는 것을 확인해서 수업시간에 배운 SQL injection을 시도하려고 하였으나 그 이후로는 몸 상태가 안 좋아져서 하지 못했습니다.