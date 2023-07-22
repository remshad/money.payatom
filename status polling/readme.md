
 
### Status Polling Integration 

This API is for polling status for a particular transaction

#### Required Fields
```sh
secret_key = given secret key;
url_of_polling_api ( you wil get it)
```
In the POST request you will pass following datails
```sh 
pid  // Merchand ID
order_id  // Unique order id which created while passing payment request
post_hash  // we will explain how to generate this
```

- step 1: create hash from given info
create md5 by appending values of order_id,pid,secret_key
```sh
#PHP Example:
 $local_hash = md5($order_id . $pid  . $row['secret_key']);
```

- step 2: Encrypt Hash 
you need to encrypt hash using secret key


```sh
#PHP Example:

$encrypted_hash=encrypt($local_hash,  $row['secret_key']);

 function encrypt($plaintext, $password) {
    $method = "AES-256-CBC";
    $key = hash('sha256', $password, true);
    $iv = openssl_random_pseudo_bytes(16);

    $ciphertext = openssl_encrypt($plaintext, $method, $key, OPENSSL_RAW_DATA, $iv);
    $hash = hash_hmac('sha256', $ciphertext . $iv, $key, true);

    return $iv . $hash . $ciphertext;
}
```


- step 3:  base64 Encode on encrypted hash for safe delivery
- 
```sh
// Compute the payment hash locally In (PHP Example)
$encoded_hash=base64_encode($encrypted_hash);   
```
- step 4: send a Post request with given url

Send a post request which contain  pid,order_id,post_hash ( as json post body ) to url_of_polling_api and you will get a response after validating data
 

```sh 
 <?php
//
// A very simple PHP example that sends a HTTP POST to a remote site
//

$data['pid']=pid;
$data['order_id']=order_id;
$data['post_hash']=order_id;

$ch = curl_init();
$url=you will get api url in the call
curl_setopt($ch, CURLOPT_URL,$url);
curl_setopt($ch, CURLOPT_POST, 1);
 

// In real life you should use something like:
curl_setopt($ch, CURLOPT_POSTFIELDS,json_encode($data,true));

// Receive server response ...
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

$server_output = curl_exec($ch);

curl_close($ch);
if($server_output){

// You can follow step 5 to prosess response

}
 
?>
```
Step 5 : Process response
you will get a json response which is having following fields
#
    order_id :  // order id 
    upi_id  : // collection upi id
    requested_amount  :  // requested amount
    received_amount : // received amount
    bank_ref : // bank refence id or UTR if available
    webhook_acknowledged : // web acknowledge status 0 or 1
    sender_upi: // sender upi id if available
    status : // payment approval status either Approved,Declined,Pending
    post_hash : // payload verification encrypted hash
    refund_info: // information about refund request

```sh
# Refund Information Details:
refunded_upi:  // refunded upi
refund_amount: // refund amount
refund_initiated_time: // time of refund
refund_completed_time: // refund complete time
refund_status: // status of refund
refund_notes: // refund notes if any
                        
```
                
```sh

#PHP Example:
                
                $data = file_get_contents("php://input");
                $row1=json_decode($data, true);
                $row1['order_id'];
                $row1['upi_id'];
                $row1['amount'];
                $row1['webhook_acknowledged'];
                $row1['status'];
                $row1['post_hash'];
            
                
                
                $encrypted_hash=base64_decode($row1['post_hash']);  // decode post hash
                $remote_hash = decrypt($encrypted_hash,$row['secret_key']); // decrypt encrypted hash
                $local_hash = md5($order_id . $data['amount'] . $data['status'] . $row['secret_key']);   // generate local hash
```
Step 5 : Verifiy response

#PHP Example:
if $local_hash equal to $remote_hash then the data is verified
```sh
if($remote_hash==$local_hash)
{
    // validated status
}else
{
 // invalid status   
}

```
