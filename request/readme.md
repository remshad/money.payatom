before heading into this part of document the developer has to read basic work flow money.payatom.in

This page describe how to send payment request to money.payatom.in

note:
all the request has to be come from white listed IP ( please make sure that your ip is white listed )


Request:

API: https://money.payatom.in/api/request.php
Method: POST
Format: Json
Encryption: None
Encoding: Plain text


Required Fields:
pid: provided MID
order_id: unique order id 
amount: integer amount in indian rupees
upi_id: sender upi id

Sample Request body :
{"pid":"2323232323","order_id":"6876mhn", "amount":"8128","upi_id":"3223mdsds@upi"}


Response:

Response Format : Json
Available Fileds:
ref_code: unique code for a partcular transaction ( please save in somewhere its needed for future refernce )
qr_code: upi generated code
status: request status

Sample Response Body:
{"ref_code":"a1c9a8d72ca3e9c3fbdb72b98110b28217260b821c27fbf5108b21f052bcfad8","qr_code":"upi:\/\/pay?pa=9d434773296296@upi&pn=TecPay&am=8128&tn=6876mhn&cu=INR&mc=5411","status":"success"}