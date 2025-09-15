
yc iam create-token

export TF_VAR_yc_token="t1.9euelZqWxsqVi5WVmsuKxpPIjY_Ine3rnpWajIqSiceLnovMj5KRiciXzprl8_dRbD86-e8GOEJN_t3z9xEbPTr57wY4Qk3-zef1656VmpaXi46OkZLNyZfKjcaRx5nG7_zF656VmpaXi46OkZLNyZfKjcaRx5nG.ObpoEo8pgE6X2Pn3tBVV1XYOBBRs2n1EWEuBFtQTqsJv0EpNNDpt_ZNVXkZM9H20WGVKCemi8TxFAQCHbtaYCg"

vim ~/.bashrc

source ~/.bashrc

providers.tf
```
provider "yandex" {  
    
  # token     = var.token  
  # service_account_key_file = file("~/keys/authorized_key.json")  # токен берется из переменной окружения TF_VAR_YC_TOKEN  token = var.yc_token  
  cloud_id  = var.cloud_id  
  folder_id = var.folder_id  
}
```

variables.tf
```
variable "yc_token" {  
  description = "IAM token for Yandex Cloud authentication"  
  type        = string  
  default     = ""  
}
```


