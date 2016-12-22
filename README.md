# About
Docker image to fetch Let's Encrypt SSL certificate using DNS acme challenge via Route53. Following projects were used:

* https://github.com/barnybug/cli53
* https://github.com/lukas2511/dehydrated
* https://github.com/whereisaaron/dehydrated-route53-hook-script

# Prerequisites

* Add domain to Route53
* Create IAM user with standard access 'AmazonRoute53FullAccess' policy or create 
custom policy to provide more restricted access. See Appendix A for example.
 
# How to use

Dry run:
```
docker run -e CA="https://acme-staging.api.letsencrypt.org/directory" \
    -e AWS_ACCESS_KEY_ID='XXXXX' \
    -e AWS_SECRET_ACCESS_KEY='XXXXX' \
    -v $(pwd)/certificates:/workbench sugdyzhekov/dehydrated-route53 \
    -d example.com -d www.example.com
```

Run
```
docker run -e AWS_ACCESS_KEY_ID='XXXXX' \
    -e AWS_SECRET_ACCESS_KEY='XXXXX' \
    -v $(pwd)/certificates:/workbench sugdyzhekov/dehydrated-route53 \
    -d example.com -d www.example.com
```

Check `certifcates` directory to obtain your certificates. You may repeat command to renew certificate. 
Feel free to add it in your cron task list. 

## Appendix A: IAM role

Example was taken from https://github.com/whereisaaron/dehydrated-route53-hook-script:


```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "route53:ListHostedZones",
                "route53:ListHostedZonesByName"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "route53:ListResourceRecordSets",
                "route53:ChangeResourceRecordSets"
            ],
            "Resource": "arn:aws:route53:::hostedzone/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "route53:GetChange"
            ],
            "Resource": "arn:aws:route53:::change/*"
        }
    ]
}
```

You could restrict this even further by listing only specific Route 53 zone Id's in, e.g.

```
"Resource": [ 
    "arn:aws:route53:::hostedzone/Z12345678901", 
    "arn:aws:route53:::hostedzone/Z12345678901" ]
```