# A Bucket of Phish 

## Challenge Description
> ![image](https://github.com/user-attachments/assets/e182fef4-d357-4910-99ed-0063680ff616)

## Solution
Upon analyzing the challenge name, "A Bucket of Phish," it hints at the use of an Amazon S3 bucket. Instead of directly accessing the website, we should investigate the associated S3 bucket for exposed files.

Using the AWS CLI, we list the contents of the S3 bucket:
```bash
aws s3 ls s3://darkinjector-phish
```

![image](https://github.com/user-attachments/assets/84e10def-fdfc-4e22-be7e-19625b4c793b)

From the output, captured-logins-093582390 seems to be a potential log file containing credentials harvested by the phishing campaign.

We download the file using:
```bash
aws s3 cp s3://darkinjector-phish/captured-logins-093582390 ./captured-logins-093582390
```

Printing the contents of the file reveals the flag:
```
THM{this_is_not_what_i_meant_by_public}
```

![image](https://github.com/user-attachments/assets/07b85d3d-b942-48b4-9103-11415008578d)

## Conclusion
Misconfigured buckets can unintentionally expose sensitive data to the public, which we were able to retrieve the flag.

 ## Flag
THM{this_is_not_what_i_meant_by_public}
