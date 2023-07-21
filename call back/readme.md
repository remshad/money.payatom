

### CallBack Integration 

Callback invoke when ever there is any status change in the transaction
Valid transaction status  are:
Approved
Declined
Late Approved
No Matching Payment for UTR
Pending
Refund Initiated
Refund Completed
User Timed Out


The most famous transaction changes are (but not limited ):
Pending => Approved
Pending => Declined
Pending => No Matching Payment for UTR
Pending => User Timed Out
User Timed Out => Late Approved
No Matching Payment for UTR => Late Approved
Declined => Refund Initiated
Refund Initiated => Refund Completed


CallBack landing page has to available at your server some secret path but it should publically avaialble from our white listed IP


#### Required Fields
```sh
secret_key = given secret key;
```
In the POST body you will get following properties in json
```sh 
order_id : your order id shared
requested_amount : requested amount
received_amount : received amount
bank_ref : bank reference / UTR if avaialble
sender_upi : sender upi id if avaialble
ref_code : unique code for the transaction
status : status of payment at this time
post_hash  : post hash for security verification
refund_info : if any refund refund data available
```

Refund Details Fields:
```sh
refunded_upi : to which upi refund has made
refund_amount : refund amount
refund_initiated_time : time of refund
refund_status : status of refund
refund_notes : any notes availabe about refund
```


Verify the integerity of recived data:

- step 1: base64 decode post_hash
```sh
capture json data availbe in post body and json decode and then pick post_hash and base64 decode 
#PHP Example:

$data = file_get_contents("php://input");
$json = json_decode($data, true);
$encrypted_hash=base64_decode($json['post_hash']);
```

- step 2: Decrypt Hash 
```sh
#PHP Example:

 function decrypt($ivHashCiphertext, $password) {
    $method = "AES-256-CBC";
    $iv = substr($ivHashCiphertext, 0, 16);
    $hash = substr($ivHashCiphertext, 16, 32);
    $ciphertext = substr($ivHashCiphertext, 48);
    $key = hash('sha256', $password, true);

    if (!hash_equals(hash_hmac('sha256', $ciphertext . $iv, $key, true), $hash)) return null;
    return openssl_decrypt($ciphertext, $method, $key, OPENSSL_RAW_DATA, $iv);
}

$remote_hash=decrypt($encrypted_hash,$secret_key);
```


- step 3:  compute hash (use md5 128 bit hashing algorithm to generate hash)
```sh
// Compute the payment hash locally In (PHP Example)
$local_hash = md5($order_id.$received_amount.$status.$secret_key);  
```
- step 4: verify hash (Compare hash given at request and local hash)
```sh 
if ($remote_hash == $local_hash)
{
  // consider received amount to update
  // Mark the transaction as success & process the order
  // You can write code process the order herer
  // Update your db with payment success
  $hash_status = "Hash Matched";
    
}
  else
  {
      // Verification failed
       $hash_status = "Hash Mismatch";
  }
```
Step 5 : Acknowledge Back payment gateway (You should  Acknowledge back payment gateway that you saved the status of payment , otherwise you will get multiple acknowledge )
```sh
$data['hash_status']=$hash_status; // 'Hash Matched' or 'Hash Mismatch' 
$data['acknowledge']=$acknowledge; // 'yes' or 'no'
header('Content-Type: application/json; charset=utf-8');
echo json_encode($data); // output as a json file
```

        Defenition of Paymennt status :
        Approved	:> Payment is Approved by our system
        Declined :>	Payment is declined by our system
        No Matching Payment for UTR	:> system waited till timeout but no payment/matching UTR received against the payment 
        Pending	:> User session in active waiting to finish payment
        User Timed Out :>	User did'nt finished payment within the session period


![image](https://user-images.githubusercontent.com/30625676/213218551-7deef0e1-2812-421c-8845-767a5207fef6.png)

