```ts
/**
 *  9장. 설명하는 상수
 * 13장. 하나의 더미 (반복되는 코드, 그 중에서도 반복되는 조건문)
 */

// As-is
const handleAddCustomerBills = () => {
    const params: AddCustomerBillsDTO = {
      billIds: selectedBillIds,
    }
    addCustomerBillsMutation.mutate(params, {
      onSuccess(data) {
        const hasSuccess = Array.isArray(data.success) && data.success.length > 0
        const hasFails = Array.isArray(data.fails) && data.fails.length > 0

        // 성공한 케이스가 있는 경우
        if (hasSuccess) {
          // 생략
        }
        // 실패한 케이스가 있는 경우
        if (hasFails) {
          data.fails.forEach((failedBill) => {
            const billIds = failedBill.entityIds.join(', ')
            switch (failedBill.httpCode) {
              // 미확정 상태인 청구서를 생성하는 경우
              case 400:
                snackbar.pushSnackbar(`${billIds}: 확정 상태로 변경하세요.`, {
                  variant: 'error',
                })
                break
              // 같은 월, 같은 고객, 같은 청구 금액으로 청구서가 이미 생성되어 있음
              case 409:
                snackbar.pushSnackbar(`${billIds}: 이미 생성된 청구서입니다.`, {
                  variant: 'error',
                })
                break
              // 고객용으로 생성된 청구서를 다시 생성하려고 하는 경우
              case 422:
                snackbar.pushSnackbar(`${billIds}: 고객용 청구서를 다시 생성할 수 없습니다.`, {
                  variant: 'error',
                })
                break
              // 	고객의 계약 관련 상품(Bill.dbo.Product)의 레코드가 존재하지 않거나 찾을 수 없는 경우
              case 424:
                snackbar.pushSnackbar(`${billIds}: 데이터가 존재하지 않거나 찾을 수 없습니다.`, {
                  variant: 'error',
                })
                break
              default:
                snackbar.pushSnackbar('청구서 생성에 실패하였습니다.', { variant: 'error' })
                break
            }
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
    const params: DeleteBillsDTO = {
      billIds: selectedBillIds,
    }
    deleteBillsMutation.mutate(params, {
      onSuccess(data) {
        const hasSuccess = Array.isArray(data.success) && data.success.length > 0
        const hasFails = Array.isArray(data.fails) && data.fails.length > 0

        if (hasSuccess) {
          // 생략
        }
        if (hasFails) {
          data.fails.forEach((failedBill) => {
            const billIds = failedBill.entityIds.join(', ')
            switch (failedBill.httpCode) {
              case 400:
                snackbar.pushSnackbar(`${billIds}: 청구서를 삭제할 수 없습니다.`, {
                  variant: 'error',
                })
                break
              case 422:
                snackbar.pushSnackbar(`${billIds}: 매입 청구서는 삭제할 수 없습니다.`, {
                  variant: 'error',
                })
                break
              default:
                snackbar.pushSnackbar('청구서 삭제에 실패하였습니다.', { variant: 'error' })
                break
            }
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