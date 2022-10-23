YAML workflow to upload files to AWS S3 bucket after each push to GitHub.

-   Must created an AWS user with permissions that allow writes to S3.
-   Save user credentials.
-   If you're using CloudFront, you'll need to add appropriate permission to invalidate cache to the same user.

-   In the directory you have being tracked by Git, create a .gitignore directory, and inside that, create a workflows directory.
-   In the .gitignore/workflows directory, create a main.yaml file where you will create the GitHub Action script.

-   In the GitHub repo, click on 'Settings' then 'Secrets' and 'Actions'.
-   You'll then need to create the appropriate secrets using the name of the S3 bucket, IAM user AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.


name: Upload Website
# This portion will upload files to S3 after each push to GitHub
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
# The following section will invalidate the CloudFront distribution, to reflect changes quickly in the browser
    - name: Invalidate CloudFront
      uses: chetan/invalidate-cloudfront-action@v2
      env:
        DISTRIBUTION: ${{ secrets.DISTRIBUTION }}
        PATHS: '/*'
        AWS_REGION: 'us-east-1'
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}