## 코드에 적용하기

- ~~handleAddCustomerBills와 handleDeleteBills는 각각 다른 파일에 존재함. (사실 다른 파일에도 있는데 일부만..) → "HTTP 에러 코드별 사용자 메시지를 처리한다" + "반복되는 코드, 그 중에서도 반복되는 조건문" ⇒ 하나로 모음~~

- 8장. 설명하는 변수 
- 9장. 설명하는 상수 (HTTP 에러 코드)

```ts
// To-be: 코드 정리 적용

// HTTP 에러 코드를 설명하는 상수로 정의
const HTTP_BAD_REQUEST = 400
const HTTP_FORBIDDEN = 403
const HTTP_NOT_FOUND = 404
const HTTP_CONFLICT = 409
const HTTP_UNPROCESSABLE_ENTITY = 422
const HTTP_FAILED_DEPENDENCY = 424

const handleAddCustomerBills = () => {
  const params: AddCustomerBillsDTO = { billIds: selectedBillIds }
  addCustomerBillsMutation.mutate(params, {
    onSuccess: (data) => {
      // 설명하는 변수
      const billSuccesses = data.success ?? []
      const billFailures = data.fails ?? []
      const hasSuccess = Array.isArray(billSuccesses) && billSuccesses.length > 0
      const hasFails = Array.isArray(billFailures) && billFailures.length > 0

      if (hasSuccess) {
        // 생략
      }
      if (hasFails) {
        billFailures.forEach((failedBill) => {
          const billIds = failedBill.entityIds.join(', ')
          let message = ''
          switch (failedBill.httpCode) {
            case HTTP_BAD_REQUEST:
              message = `${billIds}: 확정 상태로 변경하세요.`
              break
            case HTTP_CONFLICT:
              message = `${billIds}: 이미 생성된 청구서입니다.`
              break
            case HTTP_UNPROCESSABLE_ENTITY:
              message = `${billIds}: 고객용 청구서를 다시 생성할 수 없습니다.`
              break
            case HTTP_FAILED_DEPENDENCY:
              message = `${billIds}: 데이터가 존재하지 않거나 찾을 수 없습니다.`
              break
            default:
              message = '청구서 생성에 실패하였습니다.'
          }
          snackbar.pushSnackbar(message, { variant: 'error' })
        })
      }
    },
    onError(e) {
      snackbar.pushSnackbar('청구서 생성에 실패하였습니다.', { variant: 'error' })
    },
    // 생략
  })
}

const handleDeleteBills = () => {
  const params: DeleteBillsDTO = { billIds: selectedBillIds }
  deleteBillsMutation.mutate(params, {
    onSuccess: (data) => {
      const billSuccesses = data.success ?? []
      const billFailures = data.fails ?? []
      const hasSuccess = Array.isArray(billSuccesses) && billSuccesses.length > 0
      const hasFails = Array.isArray(billFailures) && billFailures.length > 0

      if (hasSuccess) {
        // 생략
      }
      if (hasFails) {
        billFailures.forEach((failedBill) => {
          const billIds = failedBill.entityIds.join(', ')
          let message = ''
          switch (failedBill.httpCode) {
            case HTTP_BAD_REQUEST:
              message = `${billIds}: 청구서를 삭제할 수 없습니다.`
              break
            case HTTP_UNPROCESSABLE_ENTITY:
              message = `${billIds}: 매입 청구서는 삭제할 수 없습니다.`
              break
            default:
              message = '청구서 삭제에 실패하였습니다.'
          }
          snackbar.pushSnackbar(message, { variant: 'error' })
        })
      }
    },
    onError(e) {
      snackbar.pushSnackbar('청구서 삭제에 실패하였습니다.', { variant: 'error' })
    },
    // 생략
  })
}
```
