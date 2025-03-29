# Cloud Sanity Check

## Challenge Description
> ![image](https://github.com/user-attachments/assets/b35fcbed-ec30-49b3-a7b1-ab6e075bb5ef)

## Approach
First, I configured the AWS CLI with the provided credentials:
```
aws configure
```
I entered the Access Key, Secret Key, and set the region to us-west-2 as instructed.

Since I wasn't sure which AWS service contained the flag, I tried listing available IAM policies:
```
aws iam list-policies --scope Local
```

Unfortunately, this returned an Access Denied error, meaning I didn't have permission to view policies.

So, I checked AWS Secrets Manager, which stores sensitive information like passwords and API keys:
```
aws secretsmanager list-secrets
```

This revealed a secret named "secret-flag", indicating it might contain the flag.

![image](https://github.com/user-attachments/assets/44dd730a-f56b-4a5b-85d7-8de35ff967fe)

To extract the secret value, I ran:
```
aws secretsmanager get-secret-value --secret-id secret-flag
```
And the flag `THM{for_your_eyes_only}` is retrieved!

![image](https://github.com/user-attachments/assets/762dcbe3-26fc-4627-9cd5-b5fe66b5c1a2)

## Conclusion
This challenge demonstrated how to use AWS CLI to interact with cloud services, particularly AWS Secrets Manager. By identifying accessible services and retrieving stored secrets, I successfully extracted the flag.

## Flag
THM{for_your_eyes_only}

   
