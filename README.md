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

### 3.3 Estructura del JWT

![api-connect-10-jwt-generate](jwt-generate.png)

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

## 5. Generate JWT (/gen) - Code

![api-connect-10-flow](api-connect-10-flow.png)

### 5.1 set-variable
```
- set-variable:
	version: 2.0.0
	title: set-variable
	actions:
	  - set: hs256-key
		value: >-
		  { "alg": "HS256", "kty": "oct", "use": "sig", "k":
		  "o5yErLaE-dbgVpSw65Rq57OA9dHyaF66Q_Et5azPa-XUjbyP0w9iRWhR4kru09aFfQLXeIODIN4uhjElYKXt8n76jt0Pjkd2pqk4t9abRF6tnL19GV4pflfL6uvVKkP4weOh39tqHt4TmkBgF2P-gFhgssZpjwq6l82fz3dUhQ2nkzoLA_CnyDGLZLd7SZ1yv73uzfE2Ot813zmig8KTMEMWVcWSDvy61F06vs_6LURcq_IEEevUiubBxG5S2akNnWigfpbhWYjMI5M22FOCpdcDBt4L7K1-yHt95Siz0QUb0MNlT_X8F76wH7_A37GpKKJGqeaiNWmHkgWdE8QWDQ",
		  "kid": "hs256-key" }
		type: string
	description: set
```

### 5.2 gatewayscript - generate private.claim
```
var apim = require('apim');

var json_privateclaim = {
	'bank': {
		'code':'200',
		'description':'ok',
		'server_code':'0',
		'server_description':'Success',
		'data':{
			'client_id':'123456789',
			'client_type':'1'
		}
	}	
};
apim.setvariable('private.claim', json_privateclaim);
```

### 5.3 jwt-generate
```
- jwt-generate:
	version: 2.0.0
	title: jwt-generate
	jwt: generated.jwt
	iss-claim: request.headers.iss-claim
	exp-claim: 60
	aud-claim: request.headers.aud-claim
	jws-jwk: hs256-key
	jws-alg: HS256
	sub-claim: request.headers.subject-claim
	private-claims: private.claim
```

### 5.4 gatewayscript - generate response
```
var apim = require('apim');
var json_output = {
	'jwt': apim.getvariable('generated.jwt')
};

apim.output('application/json');
session.output.write(json_output);
```

## 6. Validate JWT (/val) - Code

![api-connect-10-flow](api-connect-10-flow.png)

### 6.1 set-variable
```
- set-variable:
	version: 2.0.0
	title: set-variable
	actions:
	  - set: hs256-key
		value: >-
		  { "alg": "HS256", "kty": "oct", "use": "sig", "k":
		  "o5yErLaE-dbgVpSw65Rq57OA9dHyaF66Q_Et5azPa-XUjbyP0w9iRWhR4kru09aFfQLXeIODIN4uhjElYKXt8n76jt0Pjkd2pqk4t9abRF6tnL19GV4pflfL6uvVKkP4weOh39tqHt4TmkBgF2P-gFhgssZpjwq6l82fz3dUhQ2nkzoLA_CnyDGLZLd7SZ1yv73uzfE2Ot813zmig8KTMEMWVcWSDvy61F06vs_6LURcq_IEEevUiubBxG5S2akNnWigfpbhWYjMI5M22FOCpdcDBt4L7K1-yHt95Siz0QUb0MNlT_X8F76wH7_A37GpKKJGqeaiNWmHkgWdE8QWDQ",
		  "kid": "hs256-key" }
		type: string
	description: set
```

### 6.2 jwt-validate
```
- jwt-validate:
	version: 2.0.0
	title: jwt-validate
	jwt: request.headers.authorization
	output-claims: decoded.claims
	jws-jwk: hs256-key
```

### 6.3 gatewayscript - generate response
```
var apim = require('apim');
var json_output = apim.getvariable('decoded.claims');

apim.output('application/json');
session.output.write(json_output);
```
## 7. gatewayscript - catch error

```
var apim = require('apim');
var json_output = {
	'code': '404',
	'message': 'Token no existe en cache',
	'msg': apim.getvariable('jwt-validate.error-message')
};

apim.output('application/json');
session.output.write(json_output);
```