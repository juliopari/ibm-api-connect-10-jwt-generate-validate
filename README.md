# ibm-api-connect-10-jwt-generate-validate

![api-connect-10-flow](api-connect-10-flow.png)

## 1. Guide Generar JWT
```
https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=SSMNED_v10/com.ibm.apic.apionprem.doc/tutorial_onprem_jwt_gen.htm
```

## 2. Guide Validar JWT
```
https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=SSMNED_v10/com.ibm.apic.apionprem.doc/tutorial_onprem_jwt_val.htm
```

## 3. Generate JWT 

### 3.1 Request
```
curl --location 'https://{gateway-catalog}/api-jwt/gen' \
--header 'x-ibm-client-id: 123456789123456789123456789123456789' \
--header 'iss-claim: bank.com' \
--header 'aud-claim: portal' \
--header 'subject-claim: mirut123456' \
--header 'Cookie: __Host-GAPS=1:chwyB83VgqOkad5aogVgRzpgiYdgRQ:g1hXNcD6tP5CISW4'
```

### 3.2 Response
```
{
    "jwt": "Bearer eyJraWQiOiJoczI1Ni1rZXkiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJiYW5rLmNvbSIsInN1YiI6Im1pcnV0MTIzNDU2IiwiYXVkIjoicG9ydGFsIiwiZXhwIjoxNjc2OTk4NTkxLCJpYXQiOjE2NzY5OTg1MzEsImJhbmsiOnsiY29kZSI6IjIwMCIsImRlc2NyaXB0aW9uIjoib2siLCJzZXJ2ZXJfY29kZSI6IjAiLCJzZXJ2ZXJfZGVzY3JpcHRpb24iOiJTdWNjZXNzIiwiZGF0YSI6eyJjbGllbnRfaWQiOiIxMjM0NTY3ODkiLCJjbGllbnRfdHlwZSI6IjEifX19.YWoDAU0ZwjvM6DchQN94Rd6CwLrWLlQjyozbRMDhQzw"
}
```

## 4. Validate JWT

### 4.1 Request
```
curl --location 'https://{gateway-catalog}/api-jwt/val' \
--header 'x-ibm-client-id: 123456789123456789123456789123456789' \
--header 'Authorization: Bearer eyJraWQiOiJoczI1Ni1rZXkiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJiYW5rLmNvbSIsInN1YiI6Im1pcnV0MTIzNDU2IiwiYXVkIjoicG9ydGFsIiwiZXhwIjoxNjc2OTk4ODAzLCJpYXQiOjE2NzY5OTg3NDMsImJhbmsiOnsiY29kZSI6IjIwMCIsImRlc2NyaXB0aW9uIjoib2siLCJzZXJ2ZXJfY29kZSI6IjAiLCJzZXJ2ZXJfZGVzY3JpcHRpb24iOiJTdWNjZXNzIiwiZGF0YSI6eyJjbGllbnRfaWQiOiIxMjM0NTY3ODkiLCJjbGllbnRfdHlwZSI6IjEifX19._2rd4n-qsltGnz8rJmNkijV5HIaNQoaZdYD3864MLDE' \
--header 'Cookie: __Host-GAPS=1:chwyB83VgqOkad5aogVgRzpgiYdgRQ:g1hXNcD6tP5CISW4'
```

### 4.2 Response
```
{
    "iss": "bank.com",
    "sub": "mirut123456",
    "aud": "portal",
    "exp": 1676998803,
    "iat": 1676998743,
    "bank": {
        "code": "200",
        "description": "ok",
        "server_code": "0",
        "server_description": "Success",
        "data": {
            "client_id": "123456789",
            "client_type": "1"
        }
    }
}
```

### 4.3 Response token expired
```
{
    "code": "404",
    "message": "Token expirado",
    "msg": "JWT validation failed, because the JWT has expired at Tue Feb 21 2023 14:00:03 GMT-0300 (DST)."
}
```