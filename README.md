

> base URL is `https://heartbot.gg/api`  

> Current payment is 0.5$ for testing purposes, use `ETH` (Ethereum) to evade any issues until production.

# Accounts

> /check_username <span style="color:lime">`GET`</span>

checks if a username is available

Parameters:
|Parameter|Required|Unique|
|--|--|--|
|`username`|True|N/A|
* the username have to follow this to be `3-20` characters long, and only allows : english characters, underscores, and numbers

regex : `^[a-zA-Z0-9_]{2,19}$`

#### request example :
```
https://heartbot.gg/api/check_username?username=example
```

#### Responses
<span style="color:green">`success 200`</span>

```json
{
    "status":"success",
    "is_available":bool
}
```
<span style="color:maroon">`missing_username 400`</span> 

```json
{
    "status":"bad_request",
    "code":"missing_username"
}
```
---

> /register <span style="color:orange">`POST`</span>

This end point registers a user in the system, isn't limited for testing but will be, for 3 requests per day

Parameters :
|Parameter|Required|Unique|
|--|--|--|
|`email`|True|False|
|`password`|True|False|
|`username`|True|True|

* the email should follow the following regex:`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`

#### request example

```json
{
    "username":"example",
    "password":"example123",
    "email":"example@example.com"
}
```
#### Responses :
<span style="color:green">`success 200`</span>

```json
{
    "status":"success",
    "code":"confirm_email"
}
```
This response indicates that the user got registered successfully, and should confirm their email via `/verify`

<span style="color:maroon">`invalid_email 400`</span>

```
{
    "status":"error",
    "code":"invalid_email"
}
```

<span style="color:maroon">`invalid_username 400`</span>

```
{
    "status":"error",
    "code":"invalid_username"
}
```

<span style="color:maroon">`unavailable_username 200`</span>

```
{
    "status":"error",
    "code":"unavailable_username"
}
```

<span style="color:maroon">`missing_parameter 400`</span>

```
{
    "status":"bad_request",
    "code":"missing_parameter"
}
```

a required parameter is not included in the request.

---
Common Errors list from now on

|Status|Status code|Error code|Explaination|
|--|--|--|--|
|`error`|`401`|`not_logged_in`|There's no active session sent by the client|
|`error`|`401`|`account_suspended`|The Account has been suspended by the admin, contact suuport on `support@heartbot.gg`|
|`error`|`401`|`permission_denied`|The user doesn't have sufficent permissions to perform this action|
|`error`|`401`|`not_subscribed`|The user isn't subscribed, can't perform this action|
|`bad_request`|`400`|`missing_parameter`|a required parameter isn't provided|

> limits are not presented for testing purposes, but on production, they will be, and it'll return `429` for status code and `flood` for status
---

> /verify <span style="color:lime">`GET`</span>

To verify an email via the otp that has been sent automaticlly by some end-points or sent manually by `/resend_otp`

Parameters :
|Parameter|Required|Unique|
|--|--|--|
|`otp`|True|n/a|

#### request example

```
https://heartbot.gg/api/verify?otp=000000
```
#### Response codes:
<span style="color:green">`success 200`</span> : the account has been verified successfully.


<span style="color:maroon">`invalid_otp 200`</span> : The otp either false or expired.

---

> /resend_otp <span style="color:lime">`GET`</span> 

this method is used to resend an otp, for testing purposes, this method isn't limited, but it will be on production for 1 request every 5 minutes, limits will apply on account and ip.

#### request example

```
https://heartbot.gg/api/resend_otp
```

#### Response codes :
<span style="color:green">`success 200`</span> : The email has been sent successfully. 

<span style="color:maroon">`already_verified 200`</span> : The user is already verified, they can't utilize this request.


---

> /get_profile <span style="color:lime">`GET`</span> 

gets profile data, returns :

Parameters :
|Key|Type|
|--|--|
|`user_id`|string|
|`Email`|string|
|`username`|string|
|`is_subscribed`|bool|
|`is_admin`|bool|
|`is_verified`|bool
|`is_suspended`|bool|
|`subscription_due_on`|timestamp|

#### request example

```
https://heartbot.gg/api/get_profile
```

#### Response codes :
<span style="color:green">`success 200`</span>
```json
{
  "status": "success",
  
  "data": {
    "user_id": "1234",
    "email": "example@example.com",
    "username": "example",
    "is_subscribed": true,
    "is_suspended": false,
    "is_verified": true,
    "is_admin": false,
    "subscription_due_on": 1742637067
  }
}
```

---
> /change_email <span style="color:lime">`GET`</span> 

This method is used to change email, on limiting, this will be 2 requests per day.



#### request example

```
https://heartbot.gg/api/change_email?new_email=example@example.com
```


#### Responses :
<span style="color:green">`success 200`</span>

```json
{
    "status":"success",
    "code":"confirm_email"
}
```

<span style="color:green">`success 200`</span> : This indicates that a confirmation code has been sent to the new email, use `/verify` to confirm

<span style="color:maroon">`same_email 200`</span> : Nothing got updated

---
> /request_reset_password <span style="color:lime">`GET`</span> 

This method is used to reset a user's password, to which it'll require an email to send a code to, and return a `request_id` to handle the matter further


#### request example

```
https://heartbot.gg/api/request_reset_password?email=example@example.com
```

#### Responses :
<span style="color:green">`success 200`</span> : This indicates that a password reset code has been sent to the email, and a `request_id` has been made

```json
{
    "status":"success",
    "request_id":"b340daab-3381-42bb-9d80-fdf451d10e3c"
}
```

<span style="color:maroon">`invalid_email 200`</span> : This email isn't registered/verified on our system. in case that it isn't verified, the user should contact support on suuport@heartbot.gg

---
> /reset_password <span style="color:orange">`POST`</span> 

This method is used to utilize the `request_id` and confirm identity via OTP

Parameters :
|Parameter|Required|Explaination|
|--|--|--|
|`request_id`|True|Obtained from `/request_reset_password`|
|`OTP`|True|The confirmation code which was sent to the user email|
|`new_password`|True|A new password which is expected to be different from the current password|

#### request example

```
{
    "request_id":"b340daab-3381-42bb-9d80-fdf451d10e3c",
    "otp":000000,
    "new_password":"example12@"
}
```

#### Responses :
<span style="color:green">`success 200`</span> : This indicates that the password has been changed and the user may sign in now

<span style="color:maroon">`invalid_req_id 200`</span> : Wrong/Expired request id

<span style="color:maroon">`invalid_otp 200`</span> : Wrong/Expired OTP

<span style="color:maroon">`same_password 200`</span> : Password didn't change.

---
> /change_password <span style="color:orange">`POST`</span> 

This method is used to change the password within a login session.

Parameters :
|Parameter|Required|Explaination|
|--|--|--|
|`old_password`|True|The Current password to which the user provide|
|`new_password`|True|A new password which is expected to be different from the current password|

#### request example

```
{
    "old_password":"oldexample",
    "new_password":"newexample12#"
}
```

#### Responses :
<span style="color:green">`success 200`</span> : This indicates that the password has been changed and the user may sign in now

<span style="color:maroon">`invalid_req_id 200`</span> : Wrong/Expired request id

<span style="color:maroon">`same_password 200`</span> : Password didn't change.

---
> /logout <span style="color:lime">`GET`</span> 

This method is used to terminate the session from the system

#### request example

```
https://heartbot.gg/api/logout
```

#### Responses :
<span style="color:green">`success 200`</span> : The session has been terminated.

---

# Payments

---
> /get_supported_coins <span style="color:lime">`GET`</span>

This will return all the supported coins by our service to pay 

> <span style="color:goldenrod">Note</span> : <span style="color:darkgoldenrod">heartbot.gg only method of payment is crypto</span>

#### request example

```
https://heartbot.gg/api/get_supported_coins
```
#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "coins":["belbsc","uni","link","tet","maticusdce","usdcmatic","btc","egldbsc","idbsc","hbar","usdtton","injmainnet","dot","dash","usdtmatic","usdcbsc","super","maticmainnet","usdcsol","stpt","dao","xmr","chr","cusd","usdterc20","om","sei","near","apt","inj","icx","ethbsc","usdcarc20","matic","bch","bnbbsc","theta","zec","usdcarb","zroerc20","bifierc20","daiarb","usdtop","ton","tusdtrc20","usdttrc20","jetton","busdbsc","mana","wbtcmatic","trump","xrp","axs","okb","yfi","etharb","trx","ethbase","neo","sand","usdc","banana","zroarb","grt","luna","aitech","nano","stx","cake","dai","sui","algo","ponke","sxpmainnet","pivx","sfund","usdcbase","usdcop","avaxc","bera","leash","usdtsol","avax","atom","poolx","usdj","bat","ltc","tusd","egld","usdtcelo","zksync","omg","1inch","fdusdbsc","usdp","geth","id","sol","doge","ont","xlm","usdtbsc","ada","fdusderc20","usdcalgo","etc","dcr","ethlna","xaut","tko","gt","ilv","fil","mx","zen","pyusd","knc","bel","now","cgptbsc","xtz","cati","usdtarc20","1inchbsc","aave","arb","cvc","usdtarb","tlos","bone","gusd","eos","ape","injerc20","eth","eurt","usde","s"]
}
```


---
> /make_payment <span style="color:lime">`GET`</span>

This will create an order-id and return it with an address for the coin selected for 100$ (since we are in dev stage, this amount is actually 0.5$ now), in production, this will be 1 request per week.

when making a payment while a payment isn't `failed` or `expired`, it will return the previous payment data with `"old":true` in response.

to update the status of a payment, check `/check_payment` below

> <span style="color:darkred">Warning</span> : <span style="color:red">for testing, always choose ETH.</span>

Parameters :
|Parameter|Required|Explaination|
|--|--|--|
|`coin`|True|The currency which will be used to generate an address for the payment|

#### request example

```
https://heartbot.gg/api/make_payment?coin=eth
```
#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":{
        "order_id":"4f2caeeaf4754fd7a2c7d75d712ab612",
        "pay_address":"<wallet address>",
        "old":bool
    }
}
```
<span style="color:maroon">`payment_in_process 200`</span> : there's an existing payment that is currently running, if this happens, the user has to wait 25 minutes since they made the last payment.

<span style="color:maroon">`invalid_coin 200`</span> : an unsupported coin was picked

<span style="color:maroon">`unkown_error 200`</span> : contact support or wait for a while

---
> /check_payment <span style="color:lime">`GET`</span>

This will check the status of a payment and yield a result, <span style="color:red">also the payment won't get processed without this getting pulled, and we might fall into complications if the client fail to poll this correctly</span>, if that happens, instruct the user to contact support ASAP



Parameters :
|Parameter|Required|Explaination|
|--|--|--|
|`order_id`|True|The order-id you get from `/make_payment`|

#### request example

```
https://heartbot.gg/api/make_payment?order_id=4f2caeeaf4754fd7a2c7d75d712ab612
```
> []
> `waiting` state is when the payment service waiting for a transaction to happen

> `partially_paid` indicates that a payment is not fully paid yet, thus the subscription won't be activated

> success states are `finished`, `confirmed`, `sending`. in these cases, a subscription will be running for 30 days.

> a failed state says `expired` or `failed`, once the server get these states, it'll delete the payment record so the user can make a new one.

> `confirming` state is there to prevent flashing, thus it doesn't activate a subscription.
#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":"waiting"
}
```
<span style="color:maroon">`invalid_order_id 200`</span> : order either got deleted or didn't exist at all 


---

# Bot methods

---
Common Error code: 
<span style="color:maroon">`initiate_bot_first 200`</span>

---

> /available_bots_count <span style="color:lime">`GET`</span>

This will return how many bots available

#### request example

```
https://heartbot.gg/api/available_bots_count
```

#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":2
}
```

---

> /initiate_bot <span style="color:orange">`POST`</span>

This is necessary before running the bot, it initiates the bot file and settings for the bot, <span style="color:red">The bot won't run without initating the bot</span>.

|Parameter|Required|Explaination|
|--|--|--|
|`message`|True|The message which will the bot utilize for sharing, has to be less than 4000 characters|
|`sleep_timer`|True| This will be the timer between each streak of messages in seconds, has to be 60>|

#### request example

```json
{
    "message":"Hi!",
    "sleep_timer":90
}
```


#### Responses :
<span style="color:green">`success 200`</span> : the bot is ready to run

<span style="color:maroon">`invalid_sleep_timer`</span> : The sleep timer isnt an integer

<span style="color:maroon">`sleep_timer_too_short 200`</span> : message is more than 4000 characters 

<span style="color:maroon">`message_too_long 200`</span> : order either got deleted or didn't exist at all 

<span style="color:maroon">`empty_message 200`</span> : message is empty or just spaces

---

> /run_bot <span style="color:lime">`GET`</span>

This command runs the bot

#### request example

```
https://heartbot.gg/api/run_bot
```


#### Responses :
<span style="color:green">`success 200`</span> : Bot is running

<span style="color:maroon">`already_running 200`</span> : bot is already up and running

---

> /stop_bot <span style="color:lime">`GET`</span>

This command stops the bot

#### request example

```
https://heartbot.gg/api/stop_bot
```


#### Responses :
<span style="color:green">`success 200`</span> : Bot is running


---

> /get_chats <span style="color:lime">`GET`</span>

This command gets every group the chat has joined

#### request example

```
https://heartbot.gg/api/get_chats
```


#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":[
        {"chat_id":-100101010,"title":"title1"},
        {"chat_id":-100102010,"title":"title2"},
    ]
}
```


---

> /join_chats <span style="color:orange">`POST`</span>

This command gets every group the chat has joined

#### request example

```json
{
    "group_links":["@example","https://t.me/example2","https://t.me/+sudb843bfsi"]
}
```


#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":[
    {
        "chat_link":"@example",
        "joining_status":"success"
    }
    ]
}
```
<span style="color:maroon">`botflood 200`</span> : Telegram has flooded your bot, try again later

<span style="color:maroon">`stop_bot_first 200`</span> : you have to stop the bot in order to run this method

---

> /leave_chats <span style="color:orange">`POST`</span>

This command gets every group the chat has joined

#### request example

```json
{
    "chat_ids":["@example","https://t.me/example2","https://t.me/+sudb843bfsi"]
}
```

> you acquire chat ids from `/get_chats`


#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":[
    {
        {"chat_link":"@example","leaving_status":"success"}
    }
    ]
}
```
<span style="color:maroon">`botflood 200`</span> : Telegram has flooded your bot, try again later

<span style="color:maroon">`stop_bot_first 200`</span> : you have to stop the bot in order to run this method

---
# Admin
---

> Admin system work by a supreme root user

> the root admin can `suspend` an admin, `promote`, and `demote` users, while the rest of the admins can't 
---
> /admin/get_users <span style="color:lime">`GET`</span>

gets every user available (except self)

#### request example

```
https://heartbot.gg/api/admin/get_user
```


#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":[
    data={
		"user_id": "375",
		"username": "example",
		"is_subscribed": true,
		"is_suspended": false,
		"is_verified": true,
		"is_admin": false,
	}
    ]
}
```

---


> /admin/get_user_message <span style="color:lime">`GET`</span>

Gets a user message

#### request example

```
https://heartbot.gg/api/admin/get_user?user_id=1234
```


#### Responses :
<span style="color:green">`success 200`</span>
```json
{
    "status":"success",
    "data":"user message is what this is"
}
```

<span style="color:maroon">`user_not_found 200`</span> : incorrect user_id

---

> /admin/suspend <span style="color:lime">`GET`</span>

suspends a user

> suspending an admin by the root user causes the admin to lose the admin role

#### request example

```
https://heartbot.gg/api/admin/suspend?user_id=1234
```


#### Responses :
<span style="color:green">`success 200`</span>

<span style="color:maroon">`user_not_found 200`</span> incorrect user_id

<span style="color:maroon">`user_already_suspended 200`</span> the target user is already suspended

<span style="color:maroon">`insufficient_permissions 200`</span> you can't preform this action on this user

---

> /admin/unsuspend <span style="color:lime">`GET`</span>

unsuspends a user

#### request example

```
https://heartbot.gg/api/admin/unsuspend?user_id=1234
```


#### Responses :
<span style="color:green">`success 200`</span>

<span style="color:maroon">`user_not_found 200`</span> incorrect user_id

<span style="color:maroon">`user_not_suspended 200`</span> the target user is already suspended

---

> /admin/promote <span style="color:lime">`GET`</span>

Promotes a user to admin, this action preformed only by `root`

#### request example

```
https://heartbot.gg/api/admin/promote_admin?user_id=1234
```


#### Responses :
<span style="color:green">`success 200`</span>

<span style="color:maroon">`user_not_found 200`</span> incorrect user_id

<span style="color:maroon">`user_is_admin 200`</span> the target user is already an admin

---

> /admin/demote <span style="color:lime">`GET`</span>

Promotes an admin to a customer, this action preformed only by `root`

#### request example

```
https://heartbot.gg/api/admin/demote_admin?user_id=1234
```


#### Responses :
<span style="color:green">`success 200`</span>

<span style="color:maroon">`user_not_found 200`</span> incorrect user_id

<span style="color:maroon">`user_not_admin 200`</span> the target user is not an admin


























