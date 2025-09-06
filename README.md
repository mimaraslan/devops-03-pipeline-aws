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


===My Jenkins Master ============================

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

===============================

AWS EC2 makinemi dış dünyaya açtık.
Security groups kısmına gittik.
Dışarıdan 8080 portundan erişime izin verdik.

=======Java'yı kuracağız.========================

Terminale Java yaz ve enter'a bas. Açılan komutlardan birini al ve çalıştır.

sudo apt install openjdk-21-jre  -y

java --version


=======Jenkins'i kuracağız.========================

https://www.jenkins.io/doc/book/installing/linux/



sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins  -y




Aşağıdaki komutları sırasıyla çalıştıracağız.
Bu makineyi Jenkins'e adıyoruz.
Makineyi kapatıp açtığımızda Jenkins otomatik olarak çalışır durumda olacak.

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins


Bu terminal'i kapatmadım sadece o durumdan çıktım. Terminalim açık.
Ctrl + C

Terminalime bu komutu yazıp Jenkins'in admin parolasını öğrendik.
sudo cat /var/lib/jenkins/secrets/initialAdminPassword




=== My Jenkins Agent ============================
Bu makine Docker'a özeldir.


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

sudo apt update

sudo apt upgrade  -y


===============================

İç IP adının yerine bir isim vereceğiz.
sudo nano /etc/hostname

isim olarak aşağıdakini yazdık.
My-Jenkins-Agent

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.


Makineyi yeniden başlat.

sudo init 6     
ya da       
sudo reboot



=======Java'yı kuracağız.========================

Terminale Java yaz ve enter'a bas. Açılan komutlardan birini al ve çalıştır.

sudo apt install openjdk-21-jre  -y

java --version

java -version




===== Docker kuracağız. ==========================

Terminale gelip sadece docker yaz ve enter'a.

sudo apt  install docker.io  -y

sudo usermod -aG docker $USER

sudo reboot



Makineleri birbirne tanıtacağız.
=== My Jenkins Master için ============================

sudo nano  /etc/ssh/sshd_config

Authentication kısmına gel.
Aşağıdaki şu iki satırın önündeki açıklama işaretini # kaldır.


# Authentication:

PubkeyAuthentication yes

AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

sudo service sshd reload


=== My Jenkins Agent için ============================

sudo nano  /etc/ssh/sshd_config

Authentication kısmına gel.
Aşağıdaki şu iki satırın önündeki açıklama işaretini # kaldır.


# Authentication:

PubkeyAuthentication yes

AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

sudo service sshd reload



=== My Jenkins Master için ============================

pwd

cd /home/ubuntu

SADECE İÇİN
Master makinenin takip edilebilmesi için bir şifre anahtar oluşturuyorum.

ssh-keygen


cd /home/ubuntu/.ssh/

ll


sudo cat  id_ed25519.pub


İçindeki böyle yazan satırı alıp kopyalayın.

ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE9RkuXUmT8WhXeqLskDaxQi7W8pWdBJWw2VEDktsVOY ubuntu@My-Jenkins-Master



Sonuna kadar enter'a basıp geç.


=== My Jenkins Agent için ============================

cd /home/ubuntu/.ssh/

ll

sudo cat authorized_keys

Bu dosyanın için aç.
sudo nano authorized_keys

Master'dan aldığın şu satırı en alta yapıştır.
ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master


==== Agent Takip eden taraf ===
ssh-rsa BBBBBBBBBBBBBBBBBB MyAWSKeyPair

==== Master'dan getirdiğim keygen anahtar takip edilecek taraf ===
ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.


cd /home/ubuntu/.ssh/

sudo cat authorized_keys





