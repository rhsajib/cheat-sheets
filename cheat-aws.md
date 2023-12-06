### EC2

```sh
# ubuntu

chmod 600 e2e-ms-hub.pem
ssh -i e2e-ms-hub.pem ubuntu@54.206.10.228
sudo apt update
```

```sh
# for jenkins

# java runtime environment
sudo apt install openjdk-11-jre

# link: https://www.jenkins.io/doc/book/installing/linux/#debianubuntu
# install jenkins using Long Term Support release

# check jenkins status (start or not)
systemctl status jenkins

# check if jenkins is running
ps -ef | grep jenkins
```