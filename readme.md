# GitHub Actions workflow to upload files to AWS S3 bucket after each push to GitHub.

-   Must create an AWS user with permissions that allow writes to S3.
-   Save user credentials.
-   NOTE: If you're using CloudFront, you'll need to add appropriate policy with permission to invalidate cache to the same user.

-   In the GitHub repo, click on 'Settings' then 'Secrets' and 'Actions'.
-   You'll then need to create the appropriate secrets using the name of the S3 bucket, IAM user AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.
- NOTE: If you're using CloudFront, you'll need to add another secret for the CloudFront Distribution ID.

-   In the GitHub repo, click on Actions, then 'set up a workflow yourself'.
-   Copy the script at the end of this README into the ./github/workflows/main.yml file GitHub has created, and commit the new file to the repo.
-   Now that the workflow is set up, any commits that are pushed to the repo will automatically be uploaded to the corresponding S3 bucket.


## Uploads files to S3 bucket after commit is pushed
### https://github.com/marketplace/actions/s3-sync
```
name: Upload Website

on:
  push:
    branches:
    - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: jakejarvis/s3-sync-action@master
      with:
        args: --follow-symlinks --delete --exclude '.git*/*'
      env:
        SOURCE_DIR: ./
        AWS_REGION: us-east-1
        AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

## Invalidates CloudFront cache
### https://github.com/marketplace/actions/invalidate-aws-cloudfront
```
    - name: Invalidate CloudFront
      uses: chetan/invalidate-cloudfront-action@v2
      env:
        DISTRIBUTION: ${{ secrets.DISTRIBUTION }}
        PATHS: '/*'
        AWS_REGION: 'us-east-1'
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```