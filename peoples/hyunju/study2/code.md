8. 설명하는 변수
```java
//before
if(userVerif.getExpiresAt().isBefore(LocalDateTime.now())) {
    userVerif.setStatus(VerificationStatus.EXPIRED);
  verificationCodeRepository.save(userVerif);
  throw new CommonException.ExpiredVerificationIdException();
}

//after
LocalDateTime now = LocalDateTime.now();
boolean isExpired = userVerif.getExpiresAt().isBefore(now);

if (isExpired) {
  userVerif.setStatus(VerificationStatus.EXPIRED);
  verificationCodeRepository.save(userVerif);
  throw new CommonException.ExpiredVerificationIdException();
}
```
9. 설명하는 상수
```java
// before
int length = dto.newPw().length();
      StringBuilder maskingNewPassword = new StringBuilder(dto.newPw());
      for (int i = 5; i < length - 1; i++) {
        maskingNewPassword.setCharAt(i, '*');
      }
//after
private static final int MASK_START_INDEX = 5;
private static final char MASK_CHAR = '*';

int length = dto.newPw().length();
StringBuilder maskingNewPassword = new StringBuilder(dto.newPw());
for (int i = MASK_START_INDEX; i < length - 1; i++) {
maskingNewPassword.setCharAt(i, MASK_CHAR);
}
```
10. 명시적인 매개변수
11. 비슷한 코드 끼리
12. 도우미 추출
13. 하나의 더미
14. 설명하는 주석
```java
//endLocalExclusive가 현재일 보다 늦을 경우 현재일 -1일을 집계 종료일로 설정(당월 통계 구할 시 통계함수를 언제 실행하더라도 같은 결과를 얻기 위함)
    if(LocalDateTime.now().isBefore(endLocalExclusive)){
      endLocalExclusive = ym.atDay(LocalDateTime.now().minusDays(1).getDayOfMonth()).atTime(LocalTime.MAX);
    }
```
15. 불필요한 주석 지우기
```java
//before
//유효시간 체크
    if(userVerif.getExpiresAt().isBefore(LocalDateTime.now())) {
      userVerif.setStatus(VerificationStatus.EXPIRED);
      verificationCodeRepository.save(userVerif);
      throw new CommonException.ExpiredVerificationIdException();
    }
    
//after
LocalDateTime now = LocalDateTime.now();
boolean isExpired = userVerif.getExpiresAt().isBefore(now);

if (isExpired) {
        userVerif.setStatus(VerificationStatus.EXPIRED);
  verificationCodeRepository.save(userVerif);
  throw new CommonException.ExpiredVerificationIdException();
}    
```