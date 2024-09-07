
# Github-ec2-CICD-Pipeliine_Actions

This project is used to connect CICD pipeline for Github repository and amazon's ec2 instance using Github actions .




## Deployment

Step1: Create an EC2 Instance and Download the Key Pair.

Step2: Create Secrets in GitHub for the Repository

Step3: Creating your first workflow

Step4: Testing

---
You need to fill in the secrets using GitHub Secrets that you can add in your repo, read GitHub Secrets

**EC2_SSH_KEY**: This will be your .pem file which you will use to login to the instance

**HOST_DNS**: Public DNS record of the instance, it will look something like this ec2-xx-xxx-xxx-xxx.us-west-2.compute.amazonaws.com

**USERNAME**: Will be the username of the EC2 instance, usually (ubuntu)

**TARGET_DIR**: Is where you want to deploy your code. usually(home)

Store the next following code as new file  in **.github/workflows/github-actions-ec2.yml** path

```bash
  name: Push-to-EC2

# Trigger deployment only on push to main branch
on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: Deploy to EC2 on master branch push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the files
        uses: actions/checkout@v2

      - name: Deploy to Server 1
        uses: easingthemes/ssh-deploy@main
        env:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          TARGET: ${{ secrets.TARGET_DIR }}

      - name: Executing remote ssh commands using ssh key
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST_DNS }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            sudo apt-get -y update
            sudo apt-get install -y apache2
            sudo systemctl start apache2
            sudo systemctl enable apache2
            cd home
            sudo mv * /var/www/html
```

Run and test the program