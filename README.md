<img width="940" height="365" alt="image" src="https://github.com/user-attachments/assets/2d0e60c2-1347-483b-a1ec-bd371a2bdd16" />

---

# 📘 CI/CD Pipeline Documentation: EC2 + Jenkins + Ansible + Docker

## 🧱 Infrastructure Overview

- **Cloud Provider**: AWS
- **Instance Type**: `t3.micro`
- **Networking**: Default VPC, subnet, and security group
- **Operating System**: Ubuntu 22.04 LTS (or your chosen OS)
- **Public IP**: (Insert IP or DNS here)
- **Access Method**: SSH with PEM key

---

## 🔧 Software Installation

### 1. **Update System**
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. **Install Docker**
```bash
sudo apt install docker.io -y
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

### 3. **Install Ansible**
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

### 4. **Install Jenkins**
```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install openjdk-11-jdk jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

---

## 🔐 Security Group Configuration

Ensure the EC2 security group allows:
- **Port 22** – SSH
- **Port 8080** – Jenkins UI
- **Port 80/443** – Web app (if applicable)

---

## 🧪 Jenkins Job Configuration

### Job Name: `Build-and-Deploy-App`

#### **Stage 1: Clone Git Repository**
- **Repository URL**: `https://github.com/your-org/your-repo.git`
- **Branch**: `main`
- **Credentials**: SSH key or GitHub token (if private)

#### **Stage 2: Trigger Ansible Playbook**
- **Build Step**: Execute shell
```bash
ansible-playbook -i inventory deploy.yml
```

---

## 📜 Ansible Playbook: `deploy.yml`

```yaml
- hosts: localhost
  become: yes
  tasks:
    - name: Build Docker image
      docker_image:
        name: myapp
        build:
          path: /home/ubuntu/app

    - name: Run Docker container
      docker_container:
        name: myapp_container
        image: myapp
        state: started
        ports:
          - "8080:80"
```

### Inventory File: `inventory`
```
localhost ansible_connection=local
```

---

## 🐳 Dockerfile Example

```Dockerfile
FROM ubuntu:20.04
RUN apt update && apt install nginx -y
COPY . /var/www/html
CMD ["nginx", "-g", "daemon off;"]
EXPOSE 80
```

---

## ✅ Validation Steps

1. **Check Jenkins Console Output** for success.
2. **Verify Docker Container**:
   ```bash
   docker ps
   curl http://localhost:8080
   ```
3. **Access Web App** via EC2 public IP: `http://<your-ec2-ip>:8080`

---

## 🧠 Best Practices

- Use environment variables for secrets.
- Add Jenkins to the `docker` group:
  ```bash
  sudo usermod -aG docker jenkins
  ```
- Monitor EC2 resource usage—`t3.micro` is suitable for testing, not production.

---

Would you like me to generate a visual diagram of this pipeline or convert this into a Markdown README for GitHub?
