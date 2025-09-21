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

```
aws --version
```


#### MacOS

```
ls -la ~/
mv ~/Downloads/MyAWSKeyPair.pem ~/.ssh/
chmod 400 ~/.ssh/MyAWSKeyPair.pem

nano ~/.ssh/config
```

Host MyDevOpsAWS
HostName PUBLIC_IP
User ubuntu
IdentityFile ~/.ssh/MyAWSKeyPair.pem

Ctrl + O
Enter
Ctrl + X

```
ssh MyAWSKeyPair
```

<hr>

## === Makine 1: My Jenkins Master ============================

Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

===============================

İç IP adının yerine bir isim vereceğiz.
```
sudo nano /etc/hostname
```
isim olarak aşağıdakini yazdık.

My-Jenkins-Master


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

veya

Ctrl + O'ya bas.
En sonda da Enter'a bas.



Makineyi yeniden başlat.

```
sudo init 6     
```
ya da       
```
sudo reboot
```

<hr>
===============================

AWS EC2 makinemi dış dünyaya açtık.
Security groups kısmına gittik.
Dışarıdan 8080 portundan erişime izin verdik.

<hr>
======= Java'yı kuracağız.========================

Terminale Java yaz ve enter'a bas. Açılan komutlardan birini al ve çalıştır.

```
sudo apt install openjdk-21-jre  -y

java --version
```

<hr>
======= Jenkins'i kuracağız.========================

https://www.jenkins.io/doc/book/installing/linux/


```
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins  -y
```




Aşağıdaki komutları sırasıyla çalıştıracağız.
Bu makineyi Jenkins'e adıyoruz.
Makineyi kapatıp açtığımızda Jenkins otomatik olarak çalışır durumda olacak.

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```


Bu terminal'i kapatmadım sadece o durumdan çıktım. Terminalim açık.
Ctrl + C

Terminalime bu komutu yazıp Jenkins'in admin parolasını öğrendik.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```



<hr>

## === Makine 2: My Jenkins Agent ============================

Bu makine Docker'a özeldir.


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

<hr>
===============================

İç IP adının yerine bir isim vereceğiz.

```
sudo nano /etc/hostname
```

isim olarak aşağıdakini yazdık.

My-Jenkins-Agent


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.


Makineyi yeniden başlat.

```
sudo init 6     
```

ya da       

```
sudo reboot
```


<hr>
=======Java'yı kuracağız.========================

Terminale Java yaz ve enter'a bas. Açılan komutlardan birini al ve çalıştır.

```
sudo apt install openjdk-21-jre  -y

java --version

java -version
```


<hr>
===== Docker kuracağız. ==========================

Terminale gelip sadece docker yaz ve enter'a.

```
sudo apt  install docker.io  -y

sudo usermod -aG docker $USER

sudo reboot
```


<hr>
Makineleri birbirne tanıtacağız.

=== My Jenkins Master için ============================

```
sudo nano  /etc/ssh/sshd_config
```

Authentication kısmına gel.
Aşağıdaki şu iki satırın önündeki açıklama işaretini # kaldır.

<hr>
### Authentication:

```
PubkeyAuthentication yes

AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
```

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

```
sudo service sshd reload
```

<hr>
=== My Jenkins Agent için ============================

```
sudo nano  /etc/ssh/sshd_config
```
Authentication kısmına gel.
Aşağıdaki şu iki satırın önündeki açıklama işaretini # kaldır.


<hr>
### Authentication:

```
PubkeyAuthentication yes

AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
```

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

```
sudo service sshd reload
```

<hr>
=== My Jenkins Master için ============================

```
pwd

cd /home/ubuntu
```

Master makinenin takip edilebilmesi için bir şifre anahtar oluşturuyorum.

```
ssh-keygen


cd /home/ubuntu/.ssh/

ll


sudo cat  id_ed25519.pub
```

İçindeki böyle yazan satırı alıp kopyalayın.

```
ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master
```


Sonuna kadar enter'a basıp geç.


<hr>
=== My Jenkins Agent için ============================

```
cd /home/ubuntu/.ssh/

ll

sudo cat authorized_keys
```

Bu dosyanın için aç.
```
sudo nano authorized_keys
```

Master'dan aldığın şu satırı en alta yapıştır.

ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master

<hr>
==== Agent Takip eden taraf ===

ssh-rsa BBBBBBBBBBBBBBBBBB MyAWSKeyPair

<hr>
==== Master'dan getirdiğim keygen anahtar takip edilecek taraf ===

ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

```
cd /home/ubuntu/.ssh/

sudo cat authorized_keys
```

<hr>

Master ve Agent makinelerini yeniden başlat.

```
sudo reboot
```

<hr>
======================================
http://PUBLIC_IP:8080/computer/(built-in)/configure

Jenkins'i aç.
Nodes kısmına gel.

Built-In Node makinesinin içine gir.

Nodes -> Built-In Node -> Configure

Number of executors kısmını SIFIR 0 yap.



Agent makineyi Jenkins'e eklemek için

Nodes -> New node

http://PUBLIC_IP:8080/computer/new

Ona "My-Jenkins-Agent" diye bir isim verdik.
Permanent Agent olduğunu seçtik.


Jenkins'te Agent'ı eklerken onun kendi iç IP'sini alacaksın.

<hr>
====== Master Makinedeki bu anahtarı okuyup kopyalayın ve Jenkins'e gelin. Credentials için ====

```
cd  /home/ubuntu/.ssh/
sudo cat id_ed25519
```

-----BEGIN OPENSSH PRIVATE KEY-----
CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC
-----END OPENSSH PRIVATE KEY-----




<hr>
GitHub Token oluşturacağız.

https://github.com/settings/tokens

MyGitHubTokenForAWS

ghp_ABCABCABCABCABCABCABCABCABCABC


<hr>

## === Makine 3: SonarQube kurulumu ==========


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

===============================

İç IP adının yerine bir isim vereceğiz.
```
sudo nano /etc/hostname
```

isim olarak aşağıdakini yazdık.

My-SonarQube

<hr>

### ÖDEV : hostname'i tek komutla değiştirmeyi bulun.

```
sudo hostname My-SonarQube
```
<hr>


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

Makineyi yeniden başlat.
```
sudo reboot
```

<hr>
====  PostgreSQL kurulumu  =====

```

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null



sudo apt update

sudo apt-get -y install postgresql postgresql-contrib




sudo systemctl enable postgresql

sudo systemctl status postgresql


sudo passwd postgres
```
parola: 123456789


<hr>
=== Veritabanına terminalden en baş yetkili olarak giriş yapmak istiyorum.

```
su - postgres
```

parola: 123456789



```
createuser sonar

psql

ALTER USER sonar WITH ENCRYPTED password 'sonar';

CREATE DATABASE sonarqube OWNER sonar;

grant all privileges on DATABASE sonarqube to sonar;

\q

exit
```




<hr>
==== Adoptium repository ====

```
sudo bash

wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc

echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list



sudo apt update

sudo apt     install temurin-17-jdk  -y

```

Aşağıdaki komutta aynı işi yapar.

```
sudo apt-get install temurin-17-jdk  -y
```

<hr>

JDK zaten var. JRE bunu sadece örnek olsun diye kuracağız. 

```
sudo apt install openjdk-17-jre -y
```

<hr>
Java'nın alternatif sürümleri seçmek için

```
sudo update-alternatives --config java

java --version
```



<hr>

### === Linux kernel sınırlarını genişleteceğiz. ===

<hr>

== Vim ve Nano terminalden dosyaların içine yazı yazmak içindir.

```
sudo vim /etc/security/limits.conf
```

Bir şey eklemek için önce klavyeden i tuşuna bas.

<hr>
== Nano ile çalışmak daha kolaydır.

```
sudo nano /etc/security/limits.conf
```

<hr>
== Bu iki satırı dosyanın en altına ekle yapıştır.

```
sonarqube   -   nofile   65536
sonarqube   -   nproc    4096
```

çıkış için ESC tuşuna bas.
:wq  yaz ve enter'a bas.



```
sudo vim /etc/sysctl.conf
```
Bir şey eklemek için önce klavyeden i tuşuna bas.
Eklenecek bilgi aşağıdaki satır.
```
vm.max_map_count = 262144
```

Çıkış için ESC tuşuna bas.
:wq  yaz


Makineyi yeniden başlat.
```
sudo reboot
```

<hr>

### ==== Sonarqube kurulumu  =====

```
pwd

cd /opt

sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.9.0.112764.zip
```

== Ziplenmiş bir dosyayı ubuntuda açkmak için bu uygulamayı indirdim.
```
sudo apt install unzip


sudo unzip sonarqube-25.9.0.112764.zip -d/opt

pwd

sudo mv   /opt/sonarqube-25.9.0.112764    /opt/sonarqube

```


<hr>
=== sonar kullanıcı oluşturulacak ve haklar verilecek

```
sudo groupadd sonar

sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar

sudo chown sonar:sonar /opt/sonarqube -R
```

<hr>
=== Veritabanıyla bu sonar kullanıcısı konuştur

```
sudo vim /opt/sonarqube/conf/sonar.properties

sonar.jdbc.username=sonar
sonar.jdbc.password=sonar

sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

```

<hr>
=== Sonar servisini oluşturacağız.

```
sudo vim /etc/systemd/system/sonar.service
```

Aşağıdaki kodları olduğu gibi bu dosyanın içine yapıştır.


```

[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target

```

Makine açıldığında sonarqube otomatik olarak çalıştırma komutları

```
sudo systemctl enable sonar

sudo systemctl start sonar

sudo systemctl status sonar
```



<hr>
=== Log takibi ===

```
sudo tail -f /opt/sonarqube/logs/sonar.log
```

Makinenin public ip değerini al ve 9000 portundan giriş yap.
```
kullanıcı: admin
parola: admin
```
Yeni parloyı verdim.

Adana_01Adana_01


<hr>
Jenkins için token oluştur.

Administrator  -> Security

http://SONARQUBE_MAKINENIN_PUBLIC_IPsi:9000/account/security



jenkins-sonarqube-token

sqa_EEEEEEEEEEEEEEEEEEEEEEEEEEE



Jenkins içinde jenkins-sonarqube-token'ı Security Text olarak kaydettir.


SonarQube pluginlerini kur.

System tarafına gir ve SonarQube ayarlarını yap.

Tool olarak Sonar Scanner'i tanıt.

Sonar'ın kurulduğu makinenin Private IPv4 addresses değerini kopayla.


http://JENKINS_MASTER_MAKINENIN_PUBLIC_IPsi:9000/account/security






### Docker'ı Jenkins'e entegre edeceğiz.

Jenkins ->  Manage Jenkins  -> Plugins



MyDockerHubTokenForAWS

docker login -u mimaraslan  -p  dckr_pat_DDDDDDDDDDDDDDDDDDDDDDDDD




Trivy  ile imajları kontrol ediyoruz.
Jenkinsfile içine bu komutları ekledik. 

        stage("Trivy Scan") {
            steps {
                script {
                    if (isUnix()) {
                        sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mimaraslan/devops-03-pipeline-aws:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
                     } else {
                        bat ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mimaraslan/devops-03-pipeline-aws:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
                    }
                }
            }
        }
		



### Docker imageleri birikiyor. Onları Agent makineden temizlemek lazım.






// Agent makinesi zamanla dolacak. Docker şişecek dolacak. Temizlik yapmanız lazım.
// Agent makinede temizlik için yeriniz azalmışsa şu komutları kulanın lütfen.
// Hatta mümkünse bu kodları buraya uyarlayın lütfen.
                    
					
Elimizle terminalden imajları tek tek silmek komutları 

					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.10
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.11
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.12
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.13
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.14
					docker rmi mimaraslan/devops-03-pipeline-aws:latest
					
					
                    docker rmi   $(docker images --format '{{.Repository}}:{{.Tag}}' | grep 'devops-03-pipeline-aws')
                    docker container rm -f $(docker container ls -aq)
                    docker volume prune -f 





<hr>

## === Makine 4: My EKS Master Server ============================
K8s için bir makine oluşturduk. İçine Kubernetes kuracağız.


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

===============================

İç IP adının yerine bir isim vereceğiz.
```
sudo nano /etc/hostname
```
isim olarak aşağıdakini yazdık.

My-EKS-Master-Server

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

veya

Ctrl + O'ya bas.
En sonda da Enter'a bas.



Makineyi yeniden başlat.

```
sudo init 6     
```
ya da       
```
sudo reboot
```

<hr>
===============================


### AWS CLI

Web Sayfası
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html


Root kullanıcısı en yetkili yönetici moduna terminalden geç.
```
sudo su
```

1.komut
```
apt install unzip -y 
```


2.komut
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```



###  kubectl 

Web Sayfası
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html#linux_amd64_kubectl

Kubernetes 1.33 seçtik.
```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.4/2025-08-20/bin/linux/amd64/kubectl

chmod +x ./kubectl

mv kubectl /bin

kubectl version 

kubectl version  --output=yaml
```

### eksctl

Web Sayfası
https://docs.aws.amazon.com/eks/latest/eksctl/installation.html



curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp


