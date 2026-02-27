# Chapter 8: 설명하는 변수

제목만 보았을 때는, 변수 이름을 설명 하듯이 작명하는 것인가? 하는 생각이 들었는데, 그건 아니였고 객체 같은것 초기화 할 때 바로 표현식을 넘기는 경우가 있는데, 표현식이 복잡하다면 바로 넣지 말고 변수로 한번 만들고 그 다음에 넘기는 형태로 바꾸면서 변수 이름은 또 이해하기 쉬운걸로 하는 것이였다.

```go
func PostUserInvite(c *gin.Context) {
	...
	url := fmt.Sprintf(conf.User.UserInviteURL, token)
	emailTz, err := time.LoadLocation(conf.Email.Timezone)
	emailItem := EmailItem{
		From:         r.SenderEmail,
		To:           r.Email,
		URL:          url,
		Exp:          iss.In(emailTz).Format("2006-01-02 15:04:05 MST"),
		SvcName:      r.ServiceName,
		ContactEmail: r.ContactEmail,
	}
	...
}
```
```go
func PostUserInvite(c *gin.Context) {
	...
	url := fmt.Sprintf(conf.User.UserInviteURL, token)
	emailTz, err := time.LoadLocation(conf.Email.Timezone)
	expiresAt := iss.In(emailTz).Format("2006-01-02 15:04:05 MST")
	emailItem := EmailItem{
		From:         r.SenderEmail,
		To:           r.Email,
		URL:          url,
		Exp:          expiresAt,
		SvcName:      r.ServiceName,
		ContactEmail: r.ContactEmail,
	}
	...
}
```
