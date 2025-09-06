# DevOps Pipeline

## CI/CD Evreni

```

CI/CD:           (Jenkins, Git,  GitHub, GitOps,  GitHub Actions,    GitLab, GitLab CI,    Bitbucket, Bamboo)
Scripting        (Python, Bash, PowerShell)
Containers:      (Docker)
Orchestration:   (Kubernetes, Helm)
Cloud            (AWS, Azure, GCP)
Virtualization:  (VMware, VirtualBox)
IaC:             (Terraform, Ansible, CloudFormation)
Monitoring:      (Prometheus, Grafana, ELK)
```

<hr>

AWS CLI kurulacak.
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

aws --version

MacOS

ls -la ~/
mv ~/Downloads/MyAWSKeyPair.pem ~/.ssh/
chmod 400 ~/.ssh/MyAWSKeyPair.pem
nano ~/.ssh/config

Host MyDevOpsAWS
HostName 54.204.235.127
User ubuntu
IdentityFile ~/.ssh/MyAWSKeyPair.pem

Ctrl + O
Enter
Ctrl + X

ssh MyAWSKeyPair


===============================

Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

sudo apt update

sudo apt upgrade  -y


===============================

İç IP adının yerine bir isim vereceğiz.
sudo nano /etc/hostname

isim olarak aşağıdakini yazdık.
My-Jenkins-Master

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

veya

Ctrl + O'ya bas.
En sonda da Enter'a bas.



Makineyi yeniden başlat.

sudo init 6     ya da       sudo reboot

