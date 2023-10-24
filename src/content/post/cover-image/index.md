---
title: "Kaynak IP’ye göre AWS Erişiminin Engellenmesi"
description: "AWS Best Practices - Kaynak IP’ye göre AWS Erişiminin Engellenmesi"
publishDate: "24 October 2023"
updatedDate: "24 October 2023"
coverImage:
  src: "./cover.png"
  alt: ""
tags: ["aws", "cloudsecurity"]
---

AWS konsol erişimlerinde, istediğimiz IP aralığı dışında gelen AWS erişimlerini kısıtlamak isteyebilirsiniz. 

Örneğin; yalnızca VPN IP’si ile yada Ofis statik IP’si ile yapılan AWS erişimlerinde kullanıcıların kaynaklara erişmesini sağlayabilirsiniz. Bu IP’lerin dışında yapılan isteklerde servislere erişim aktif olmayacaktır. 

Bir IAM policy oluşturacağız yada mevcut bir policy’i güncelleyeceğiz. Bakınız: [create a policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) or [edit a policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html).

Önemli bir ayrıntı; bu policy hiçbir servise izin vermez bu yüzden bu policy’nin yanında kullanıcıların ilgili servislere erişmesi için bir policy daha eklemeniz gerekecek.

Eğer AWS CloudShell kullanıyorsanız, bu servis belirlediğiniz IP aralıklarında çalışmaz dolayısıyla  AWS CloudShell istekleri engellenecektir. Bu durumu yaşamamak için policy’e aşağıdaki koşulu eklemeniz yeterlidir.

```json
"StringNotLike": {
  "aws:userAgent": "*exec-env/CloudShell*"
}
```

Asıl policy’imizin yapısı aşağıdaki gibi olmalıdır. 

```json
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Deny",
        "Action": "*",
        "Resource": "*",
        "Condition": {
            "NotIpAddress": {
                "aws:SourceIp": [
                    "192.0.2.0/24",
                    "203.0.113.0/24"
                ]
            },
            "Bool": {"aws:ViaAWSService": "false"}
        }
    }
}
```

Bu kısımdaki IP’leri istediğiniz aralıkta yada tek bir IP ile değiştirebilirsiniz.

```json
  "aws:SourceIp": [
                    "192.0.2.0/24",
                    "203.0.113.0/24",
                    "203.0.113.11/32"
                ]
```

Eğer belirtilen IP’lerden giriş yapmazsanız, örnek olarak EC2 menüsünde şöyle bir ekran ile karşılaşacaksınız.

![EC2 Dashboard](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5fdz1mhs6dvln5b5ljxb.png)

Sonuç olarak, bu policy ile ekstra bir güvenlik katmanı daha eklemiş olduk. Özellikle CLI erişimlerinde böyle bir policy kullanmak sizi biraz da olsa güvende hissettirebilir. 😊

to be continued …
