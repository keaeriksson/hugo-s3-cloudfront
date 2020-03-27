# Hugo Without Trailing Slashes on AWS S3 + CloudFront

This repository contains an example on how to remove trailing slashes when hosting a [Hugo](https://gohugo.io) site on AWS S3 + CloudFront.

## Prerequisites

- `hugo >= 0.60`
- `aws cli >= 1.16.287`
- Correct AWS credentials configured on your machine ([guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html))

## What is does

- Serves your static site without trailing slashes (`example.com/slash` will return the same content as `example.com/slash/`)
- Redirects URLs with trailing slashes to URLs without trailing slashes
- Removes the trailing slashes from canonical links, stub pages and sitemap
- Redirects www to naked URL (`www.example.com` -> `example.com`)
- Removes trailing slashes from sitemap, canonical links and page stubs

## Deploy Infrastructure

To deploy the bucket, CloudFront distribution and Route53 records needed to host your Hugo site run the following command from the repository root:

```
aws cloudformation deploy --stack-name <STACK_NAME> --template-file template.yaml --capabilities CAPABILITY_IAM
```

> Note: This template assumes that you have your domain hosted on Route53. Feel free to remove the record set from the template and set your DNS-records in your preferred domain provider.

## Deploy Site

To deploy your site to the bucket you just deployed run the following commands:

1. Build files `hugo`
2. Sync files to S3 `aws s3 sync ./public s3://<BUCKET_NAME> --delete --acl bucket-owner-full-control --acl public-read --cache-control max-age=31536000,public`
3. Invalidate the CloudFront cache `aws cloudfront create-invalidation --distribution-id <DISTRIBUTION_ID> --paths '/*'`
4. Done!


