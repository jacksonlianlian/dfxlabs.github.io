OpenApi Key
=============


**What are 'Public Key' and 'Api Key'**

Public key is your local public key generated using the ed25519 algorithm. The private key is kept by you. DFX will never ask you for the private key at any time. Api key is the identity assigned to you after you upload the public key to dfx.

**1. How do you generate public and private keys?**

To generate an Ed25519 key pair in the command line and convert the private key to a Base64 encoded string, you can use the OpenSSL tool.
Here's a way to generate an Ed25519 public and private key in the MacOS command line and convert it to a Base64-encoded string:


* 1.Generate private key + output content to console:

	```javascript
	openssl genpkey -algorithm ed25519 -outform DER -out private_key.der
	openssl base64 -in private_key.der
    ```

	```javascript
	If you are prompted "Algorithm ed25519 not found" after executing the command,
	this error may be because your OpenSSL version does not support the Ed25519 algorithm.
	Ed25519 is a relatively new encryption algorithm that requires OpenSSL version 1.1.1
	or above to support it.
	Please make sure your version of OpenSSL meets the requirements. If not, you can try to
	upgrade OpenSSL or use other tools that support the Ed25519 algorithm.
    ```
* 2.You will get the following content:

	```javascript
	MC4CAQAwBQYDK2VwBCIEIMAy/4rOEzkBqHLbb/zDxU9PKTKoY0F6kd+36/NYkLut
	```

* 3.Generate the public key + output the content to the console:
	```javascript
	openssl pkey -in private_key.der -pubout -outform DER -out public_key.der
	openssl base64 -in public_key.der
	```
* 4.You will get the following content:

	```javascript
	MCowBQYDK2VwAyEAvge+jH1JYA576Z3uxZFbSu13diBFn3jFfsiglRBJbTM=
	```

### Api Key permission

| Url | Method | What roles can access |Describe|
| --- | --- | --- |--- |
|/api/v1/spot/depth| GET| ALL|
|/api/v1/symbols| GET| ALL|
|/api/v1/spot/trade| GET| ALL|
|/api/v1/spot/kline| GET| ALL|
|/api/v1/spot/ticker/24hr| GET| ALL|
|/api/v1/spot/ticker/price| GET| ALL|
|/api/v1/spot/ticker/book_ticker| GET| ALL|
|/ws/**| GET| ALL| Websocket|
|/api/v1/user/info| GET| Administrator、Trader|
|/api/v1/account/balance| GET| Administrator、Trader|
|/api/v1/account/trades| GET| Administrator、Trader|Check account transaction｜
|/api/v1/spot/test/order| POST| Administrator、Trader|Test whether the order can be created｜
|/api/v1/spot/order| POST| Administrator、Trader|Create order|
|/api/v1/spot/order| DELETE| Administrator、Trader|Cancel order|
|/api/v1/spot/order| GET| Administrator、Trader|Search order|
|/api/v1/spot/open_order| GET| Administrator、Trader|Get pending orders|
|/api/v1/spot/finish_order| GET| Administrator、Trader|Get completed orders|


#### Upload the Public Key to DFX

  Congratulations, you have completed most of the steps and victory lies ahead. Next, you need to upload the ciphertext of the generated public key to the DFX API. The specific steps are as follows:

### step 1
![Image title](https://dfx-test-public.s3.ap-east-1.amazonaws.com/image/step-1.png)

### step 2
![Image title](https://dfx-test-public.s3.ap-east-1.amazonaws.com/image/step-2.png)

### step 3
![Image title](https://dfx-test-public.s3.ap-east-1.amazonaws.com/image/step-3.png)

### step 4
![Image title](https://dfx-test-public.s3.ap-east-1.amazonaws.com/image/step-4.png)



#### Use ‘Api-Key’ as ‘EXCHANGE-API-KEY’ in the request header

#### Example: Add EXCHANGE-API-KEY to Headers

``` java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class HttpGetExample {
	public static void main(String[] args) throws IOException {
		String url = "https://$HOST/api/v1/symbols?clientType=OP";
		HttpURLConnection connection = (HttpURLConnection) new URL(url).openConnection();
		connection.setRequestMethod("GET");

		// Set request header
		connection.setRequestProperty("EXCHANGE-API-KEY", "YOUR API-KEY");
		connection.setRequestProperty("EXCHANGE-API-TIMESTAMP", "1711351755000");
		connection.setRequestProperty("EXCHANGE-API-SIGN", signResult);


		int responseCode = connection.getResponseCode();
		System.out.println("Response Code: " + responseCode);

		BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));
		StringBuilder response = new StringBuilder();
		String line;
		while ((line = reader.readLine()) != null) {
			response.append(line);
		}
		reader.close();

		System.out.println("Response Body: " + response.toString());

		connection.disconnect();
	}
}
```
