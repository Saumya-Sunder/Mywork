# Supported S3 IAM REST APIs and CLI Operations

The S3 IAM REST APIs are a collection of resources and methods which are integrated with AWS Identity and Access Management (IAM) service. The IAM service lets an administrator to securely control access to the S3 resources. As an IAM administrator, you can control the authentication and authorizations for an ID to use API Gateway resources. We've listed the S3 IAM REST APIs and CLI operations that are supported by the CORTX-S3 Server Submodule. 

<details>
<summary>CreateAccount</summary>
<p>

The CreateAccount request lets you create an S3 IAM account. 

| Request | Request body attributes  | Request Parameters    |
| :------- | :------------------------ | :--------------------- |
| POST / HTTP/1.1  </br> Host: <IAM Endpoint>:9443 | **Action:** CreateAccount </br> **AccountName:** newrandom10 </br> **Email:** newrandom10@xyz.com  | <ul> <li> **AccountName:** The name of the account. This parameter allows </br>(through its regex pattern) a string of characters consisting of upper </br> and lowercase alphanumeric characters with no spaces. </br> You can also include any of the following characters:`_+=,.@-` </br> **Type:** String </br> **Length Constraints:** Minimum length of 1. Maximum length of 64. </br> **Pattern:** `[\w+=,.@-]+` </br> **Required:** Yes </br> </ul> <ul> <li> **Email:** The email of the account which you want to create. </br> **Type:** String </br> **Pattern:** `[\w+=,.@-]+` </br> **Required:** Yes |

</p>
</details>

<details>
<summary>ListAccounts</summary>
<p>
  
 The ListAccounts parameter lists all the S3 IAM accounts.

| Request | Request body attributes  | Request Parameters    |
| :------- | :------------------------ | :--------------------- |
| POST / HTTP/1.1  </br> Host: <IAM Endpoint>:9443 | **Action:** ListAccounts | None |


</p>
</details>

<details>
<summary>ResetAccountAccessKey</summary>
<p>
  
 The ResetAccountAccessKey parameter lets you reset the access key for your S3 IAM account. 
 
| Request | Request body attributes  | Request Parameters    |
| :------- | :------------------------ | :--------------------- |
| POST / HTTP/1.1  </br> Host: <IAM Endpoint>:9443 | **Action:** ResetAccountAccessKey </br> **AccountName:** newrandom6 </br> **Email:** None | <ul> <li> **AccountName:** The name of the account. This parameter allows </br>(through its regex pattern) a string of characters consisting of upper </br> and lowercase alphanumeric characters with no spaces. </br> You can also include any of the following characters:`_+=,.@-` </br> **Type:** String </br> **Length Constraints:** Minimum length of 1. Maximum length of 64. </br> **Pattern:** `[\w+=,.@-]+` </br> **Required:** Yes </br> </ul> <ul> <li> **Email:** The email of the account which you want to create. </br> **Type:** String </br> **Pattern:** `[\w+=,.@-]+` </br> **Required:** Yes |

</p>
</details>

<details>
  <summary>DeleteAccount</summary>
  <p>
    
 The DeleteAccount parameter lets you delete your S3 IAM account.
 
| Request | Request body attributes  | Request Parameters    |  
| :------ | :----------------------- | :-------------------- | 
| POST / HTTP/1.1  </br> Host: <IAM Endpoint>:9443 | **Action:** DeleteAccount </br> **AccountName:** newrandom6 </br> **Response:** Account Deleted successfully. </p> | **AccountName:** The name of the account. </br> This parameter allows (through its regex pattern) </br> a string of characters consisting of upper and </br> lowercase alphanumeric characters with no spaces. </br> You can also include any of the following characters:`_+=,.@-` </br> **Type:** String </br> **Length Constraints:** Minimum length of 1. Maximum length of 64. </br> **Pattern:** `[\w+=,.@-]+` </br> **Required:** Yes </br> </ul> |

</p>
</details>
 
<details>
  <summary>CreateAccountLoginProfile</summary>
  <p>
  
  The CreateAccountLoginProfile parameter creates a password for the specified account.
  
| Request | Request body attributes  | Request Parameters    |  
| :------ | :----------------------- | :-------------------- | 
| POST / HTTP/1.1  </br> Host: <IAM Endpoint>:9443 | **Password:** Random12@# </br> **AccountName:** newrandom10 </br> **PasswordResetRequired:** false </br> **Action:** CreateAccountLoginProfile |  **Password:** The new password for the Account. </br> **Type:** String </br> **Length Constraints:** Minimum length of 1. Maximum length of 128. </br> **Pattern:** [\u0009\u000A\u000D\u0020-\u00FF]+ </br> **Required:** Yes </br> `password-reset-required` `no-password-reset-required`: Specifies whether you're required to  set a new password during yournext sign-in. </br> **Required:** No </br> If you are passing  `--password-reset-required` and `--no-password-reset-required` argument in same command without any sequence, the `PasswordResetRequired` flag will be set as true by default. </br> **AccountName:** The name of the Account to create a password for. The Account must already exist. </br> **Type:** String </br> **Required:** Yes </br> **AccountLoginProfile:** A structure containing the account name and password create date. </br> **AccountName:** A string, that holds the name of the account used to sign in to the S3 Management Console. </br> **CreateDate:** Is a timestamp for the date when the password for the account was created. </br> **PasswordResetRequired:** is a boolean value that specifies whether the account user is required to set a new password on next sign-in. |

**Sample Response:**

b `<?xml version="1.0" encoding="UTF-8" standalone="no"?><CreateLoginProfileResponse
xmlns="https://iam.seagate.com/doc/2010-05-08/"><CreateLoginProfileResult>
<LoginProfile>
<UserName>user01</UserName>
<PasswordResetRequired>true</PasswordResetRequired>
<CreateDate>20201211090307Z</CreateDate>
</LoginProfile>
</CreateLoginProfileResult>
<ResponseMetadata>
<RequestId>88e92110820f423092a2d228316bfb10</RequestId></ResponseMetadata></CreateLoginProfile
Response>`

</p>
</details>
