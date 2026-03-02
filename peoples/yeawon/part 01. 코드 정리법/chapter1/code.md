## 코드에 적용하기 1

### As-is
```tsx
  useEffect(() => {
    if (customerData) {
      if (isAddMode) {
        resetFormForAddMode()
      } else {
        addEditForm.reset({
          serialNo: customerData.serialNo,
          tenantId: customerData.tenantId,
          contractorId: customerData.contractorId,
          contracteeId: customerData.contracteeId,
          contracteeName: customerData?.contracteeName,
          type: customerData?.type,
          isActive: customerData?.isActive,
          remark: customerData?.remark,
          customerContactName: customerData?.customerContactName,
          customerContactPhone: customerData?.customerContactPhone,
          customerContactEmail: customerData?.customerContactEmail,
          regNo: customerData?.regNo,
          configs: customerData.configs as ServiceHierarchyCustomerFormModel['configs'],
        })
        handleBeginDateChange(
          customerData?.contractDate ? dayjs(customerData.contractDate).toDate() : new Date()
        )
        handleEndDateChange(
          customerData?.expireDate ? dayjs(customerData.expireDate).toDate() : new Date()
        )
      }
    }
  }, [customerData, isAddMode])
```

### To-be (Early Return 적용)
```tsx
  useEffect(() => {
    // early return
    if (!customerData) return

    // early return: 추가 모드 처리 후 즉시 종료
    if (isAddMode) {
      resetFormForAddMode()
      return
    }

    // 수정 모드 처리 (else 제거)
    addEditForm.reset({
      serialNo: customerData.serialNo,
      tenantId: customerData.tenantId,
      contractorId: customerData.contractorId,
      contracteeId: customerData.contracteeId,
      contracteeName: customerData?.contracteeName,
      type: customerData?.type,
      isActive: customerData?.isActive,
      remark: customerData?.remark,
      customerContactName: customerData?.customerContactName,
      customerContactPhone: customerData?.customerContactPhone,
      customerContactEmail: customerData?.customerContactEmail,
      regNo: customerData?.regNo,
      configs: customerData.configs as ServiceHierarchyCustomerFormModel['configs'],
    })
    handleBeginDateChange(
      customerData?.contractDate ? dayjs(customerData.contractDate).toDate() : new Date()
    )
    handleEndDateChange(
      customerData?.expireDate ? dayjs(customerData.expireDate).toDate() : new Date()
    )
  }, [customerData, isAddMode])
```

---

## 코드에 적용하기 2

### As-is
```tsx
  useEffect(() => {
    configFields.forEach((field) => {
      const configItem = configs.find((config) => config.code === field.code)
      if (configItem) {
        if (
          (field.code === PAYMENT_METHOD_CODE ||
            field.code === EXTRA_DISCOUNT_DESCRIPTION_CODE ||
            field.code === INDIRECT_EXTRA_DISCOUNT_DESCRIPTION_CODE) &&
          configItem.valueString !== undefined
        ) {
          setValue(field.code as any, configItem.valueString)
        } else {
          setValue(field.code as any, configItem.value)
        }
      }
    })
  }, [configs])
```

### To-be (Early Return 적용)
```tsx
  useEffect(() => {
    configFields.forEach((field) => {
      const configItem = configs.find((config) => config.code === field.code)
      
      // early return
      if (!configItem) return

      // early return, 선언과 초기화를 함께 옮기기(Chapter7)
      const isValueStringField = 
        field.code === PAYMENT_METHOD_CODE ||
        field.code === EXTRA_DISCOUNT_DESCRIPTION_CODE ||
        field.code === INDIRECT_EXTRA_DISCOUNT_DESCRIPTION_CODE

      if (isValueStringField && configItem.valueString !== undefined) {
        setValue(field.code as any, configItem.valueString)
        return
      }

      // 일반 필드 처리 (else 제거)
      setValue(field.code as any, configItem.value)
    })
  }, [configs])
```