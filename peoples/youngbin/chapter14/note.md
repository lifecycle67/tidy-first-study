# Chapter 14: 설명하는 주석
기존 코드를 보면서 이해가 잘 안 되다가 나중에 이해가 되서 주석을 남긴 경험은 생각보다 잘 없는 것 같고, 누군가가 먼저 남겨둔 주석을 보고 코드를 이해하는 데 도움을 받은 적은 자주 있는 것 같다.
책에서 말한 목적의 주석을 남기는 경우는, 오히려 수정을 하다가 잘 안되고, 그러다가 다른 작업을 먼저 해야할 때 해본 것들이나 해야 할 내용 등을 남겨 둔 적이 있었다.
이럴때 필요하면 TODO 나 FIXME 등을 앞에 넣어서 달기도 했던 것 같다. 그러면 IDE 마다 좀 다르지만, 이런것만 취합해서 보여주는 기능이 있는 경우 찾아다가 확인하기 유용했던 경험이 있다.


예를 들면 이런 주석이 달려 있다면, 기존 코드를 보면서 맥락을 알 수 있어 이해에 도움이 된다.
```go
func createComment(id int) error {
	conf, _ := config.GetConfig()
	var ticketComment models.TicketComment

	//코멘트를 달 때 티켓의 상태를 지정한다. pending = 보류, hold = 대기
	ticketComment.Ticket.Comment.Body = conf.CommentMessage
	ticketComment.Ticket.Comment.Public = true
	ticketComment.Ticket.Status = conf.CommentStatus
	...
}
```

이런것 또한 코드에 대한 맥락이 제공되어 도움이 된다.
```go
//젠데스크에서는 시간처리가 UTC 기준이므로 KST로 변경한다.
	for i := range data.Results {
		data.Results[i].Created_DT = data.Results[i].Created_DT.In(time.FixedZone("KST", 9*60*60))
		data.Results[i].Updated_DT = data.Results[i].Updated_DT.In(time.FixedZone("KST", 9*60*60))
	}
```
아마 맥락을 좀 더 넣어서 이렇게 작성 해 볼 수도 있겠다.
```go
//젠데스크에서는 시간처리가 UTC 기준이므로 KST로 변경한다. DB 에 저장 할 떄 사용하는 컬럼은 KST 기준이기 때문.
	for i := range data.Results {
		data.Results[i].Created_DT = data.Results[i].Created_DT.In(time.FixedZone("KST", 9*60*60))
		data.Results[i].Updated_DT = data.Results[i].Updated_DT.In(time.FixedZone("KST", 9*60*60))
	}
```
