# Ansible Configuration Management Setup (Automate Project 7 to 10)

## Update the Name Tag on Jenkins EC2 Instance

1. Update the Name tag on your Jenkins EC2 Instance to `Jenkins-Ansible`.
    - **Screenshot**

---

## Install and Configure Ansible on EC2 Instance

### Step 1: Connect to Your EC2 Instance
1. In the AWS Console, go to **EC2** and locate your instance for Jenkins-Ansible.
2. Copy the **Public IP** of the instance.
3. Use SSH to connect to your instance:
   ```bash
   ssh -i path_to_your_key.pem ubuntu@your_instance_public_ip
   ```
   - **Screenshot**

4. Once connected, update the instance:
   ```bash
   sudo apt update
   ```
   - **Screenshot**

### Step 2: Install Ansible
1. Install Ansible directly on the instance:
   ```bash
   sudo apt install ansible -y
   ```
   - **Screenshot**

2. Check the Ansible version to confirm the installation:
   ```bash
   ansible --version
   ```
   - **Screenshot**

---

## Configure Jenkins for Archiving Repository Content

### Step 1: Create a GitHub Repository
1. Go to your GitHub account and create a new repository named `ansible-config-mgt`.
   - **Screenshot**

### Step 2: Set Up a New Freestyle Project in Jenkins
1. Open Jenkins at `http://your_instance_public_ip:8080`.
   - **Screenshot**

2. Click **New Item** on the Jenkins dashboard, name the project `ansible`, select **Freestyle project**, and click **OK**.
   - **Screenshot**

3. In the **Source Code Management** section, select **Git**.
4. Paste the URL of your `ansible-config-mgt` GitHub repository and add your GitHub credentials.
   - **Screenshot**

5. Allocate an **Elastic IP** to the Jenkins-Ansible server to maintain a consistent IP address.
   - **Screenshot**

### Step 3: Set Up a GitHub Webhook to Trigger Jenkins Build
1. In GitHub, go to **Settings** for `ansible-config-mgt` repository.
2. Under **Webhooks**, click **Add webhook**.
3. Set **Payload URL** to `http://your_instance_public_ip:8080/github-webhook/`.
4. Set **Content type** to `application/json`.
5. Choose **Just the push event** to trigger the webhook.
6. Click **Add webhook**.
   - **Screenshot**

---

## Configure Jenkins to Archive Build Artifacts

1. Go back to Jenkins and click **Configure** on the project.
2. Scroll down to **Post-build Actions**.
3. In **Post-build Actions**, select **Archive the artifacts**.
4. In **Files to archive**, type `**` to archive all files.
5. Check **GitHub hook trigger for GITScm polling**.
6. Click **Save** to finish configuring the Jenkins job.
   - **Screenshot**

---

## Test the Setup

1. In your `ansible-config-mgt` GitHub repository, edit `README.md` (e.g., add a new line).
2. Commit the changes directly to the master branch.
   - **Screenshot**

3. In Jenkins, you should see a build triggered automatically.
   - **Screenshot**

4. After the build completes, verify that the artifacts are archived in:
   ```bash
   ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
   ```
   Replace `<build_number>` with the specific build number.
   - **Screenshot**

---

## Clone Repository to Jenkins-Ansible EC2 Instance

1. On the EC2 instance terminal, navigate to the desired directory.
2. Clone the `ansible-config-mgt` repository:
   ```bash
   git clone <repository-link>
   ```
   Replace `<repository-link>` with the GitHub repository URL.
   - **Screenshot**

---

## Prepare Development Environment with Visual Studio Code

1. Download and install **Visual Studio Code** on your local machine.
2. Open Visual Studio Code and configure Git integration:
   - Go to **View > Terminal**.
   - Navigate to the directory where the repository is located.
3. Clone the repository locally:
   ```bash
   git clone <repository-link>
   ```
   - **Screenshot**

---

## Set Up Branch Structure

1. Navigate to `ansible-config-mgt` on GitHub.
2. Go to **Branches** and click **New Branch**.
3. Name the branch descriptively, such as `feature/setup-inventory-playbooks`.
4. Create and checkout the branch locally:
   ```bash
   git pull origin main
   git checkout -b feature/setup-inventory-playbooks
   ```
   - **Screenshot**

### Create Directory Structure for Ansible Files
1. Within the repository folder, create the following directories:
   ```bash
   mkdir playbooks
   mkdir inventory
   ```
   - **Screenshot**

2. Create an empty playbook file named `common.yml` inside `playbooks`.
   ```bash
   cd playbooks
   touch common.yml
   ```
   - **Screenshot**

---

## Set Up Ansible Inventory

1. In Visual Studio Code, create 4 files in the `inventory` folder named `dev`, `staging`, `uat`, and `prod`.
   - **Screenshot**

2. Edit the **Development Inventory File (dev)** to define hosts:
   ```ini
   [nfs]
   <NFS-Server-Private-IP-Address> ansible_ssh_user=ec2-user

   [webservers]
   <Web-Server1-Private-IP-Address> ansible_ssh_user=ec2-user
   <Web-Server2-Private-IP-Address> ansible_ssh_user=ec2-user

   [db]
   <Database-Private-IP-Address> ansible_ssh_user=ec2-user 

   [lb]
   <Load-Balancer-Private-IP-Address> ansible_ssh_user=ubuntu
   ```
   - **Screenshot**

---

## Set Up SSH Agent and Import Private Key

1. Start the SSH Agent:
   ```bash
   eval ssh-agent -s
   ```
   - **Screenshot**

2. Navigate to `.ssh` directory and list files to locate the private key.
   ```bash
   cd ~/.ssh
   ls
   ```
   - **Screenshot**

3. Add the private key:
   ```bash
   ssh-add ~/.ssh/id_rsa
   ```
   - **Screenshot**

4. Test the connection:
   ```bash
   ssh ubuntu@<your-public-ip>
   ```
   - **Screenshot**

---

## Update and Push Changes to GitHub

1. Add and commit the changes to Git:
   ```bash
   git add .
   git commit -m "Set up Ansible directory structure and development inventory"
   ```
2. Push changes to the feature branch:
   ```bash
   git push origin feature/setup-inventory-playbooks
   ```
   - **Screenshot**

---

## Create a Pull Request on GitHub

1. On GitHub, go to your repository and create a pull request for your recent changes.
2. Provide a descriptive title and comment about the changes.
3. Review and merge the pull request.
4. Pull the latest changes to your local master branch:
   ```bash
   git checkout master
   git pull origin master
   ```
   - **Screenshot**

---

## Configure and Test Ansible Playbook

1. Update `playbooks/common.yml` with the following configuration:
   ```yaml
   ---
   - name: update web, nfs, and db servers
     hosts: webservers, nfs, db
     become: yes
     tasks:
       - name: ensure wireshark is at the latest version
         yum:
           name: wireshark
           state: latest

   - name: update LB server
     hosts: lb
     become: yes
     tasks:
       - name: Update apt repo
         apt: 
           update_cache: yes

       - name: ensure wireshark is at the latest version
         apt:
           name: wireshark
           state: latest
   ```

---

## Additional Tasks in Ansible Playbook

1. Add the following additional tasks to create a directory, change the timezone, and run a shell script:
   ```yaml
   - name: additional tasks for all servers
     hosts: all
     become: yes
     tasks:
       - name: Create a directory
         file:
           path: /tmp/mydirectory
           state: directory

       - name: Create a file in the new directory
         file:
           path: /tmp/mydirectory/myfile.txt
           state: touch

       - name: Change timezone to UTC
         command: timedatectl set-timezone UTC

       - name: Run a shell script
         shell: /path/to/your/script.sh
   ```
   - **Screenshot**

---

## Connect to Jenkins-Ansible EC2 from Visual Studio Code

1. Open Visual Studio Code and install the **Remote - SSH** extension.
   - **Screenshot**

2. Press `F1`, type `Remote-SSH: Connect to Host`, and enter:
   ```plaintext
   ssh ubuntu@<your-public-ip>
   ```
3. Edit files directly on the server as needed.
   - **Screenshot**